---
layout: default
categories: blog
tags: elixir httpd server
title: Trying out Elixir for simple http
---

Elixir sounds like it will solve many of the worries I have as a ruby developer,
concurrency comes out-the-box and there seems to be good drive behind it.

So I want to give it a try... googled-up a guide and learnt some stuff (credit: http://crypt.codemancers.com/posts/2016-01-15-visualizing-parallel-requests-in-elixir/)

Start a new project:

    $ mix new try_api --sup

In ./mix.exs we should reference *cowboy* and *plug*

    def application do
      [applications: [:logger, :cowboy, :plug],
       mod: {HttpApi, []}]
    end

    defp deps do
      [
        {:cowboy, "~>1.0.4"},
        {:plug, "~>1.2.0"}
      ]
    end

Fetch hex dependacys:

    $ mix deps.get

Now lets define out router in lib/try_api/router.ex

    defmodule TryApi.Router do
      use Plug.Router

      plug :match
      plug :dispatch

      get "/" do
        send_resp(conn, 200, "Hello Fish")
      end

      match _ do
        send_resp(conn, 404, "Fail Whale")
      end
    end

Now lets make our application run using a Supervisor in lib/try_api.ex

    defmodule TryApi do
      use Application

      def start(_type, _args) do
        import Supervisor.Spec, warn: false

        children = [
          # `start_server` function is used to spawn the worker process
          worker(__MODULE__, [], function: :start_server)
        ]
        opts = [strategy: :one_for_one, name: TryApi.Supervisor]
        Supervisor.start_link(children, opts)
      end

      # start the cowboy server
      def start_server do
        { :ok, _ } = Plug.Adapters.Cowboy.http TryApi.Router, [], [acceptors: 2]
      end
    end

You can compile and load in console like so

    $ iex -S mix

You can use the little gui to get xray vision into your application

    iex(6)> :observer.start

Try requesting 5,000 api responses .. no problems!

    iex(6)> for n <- 1..5000, do: spawn(fn -> :httpc.request('http://localhost:4000') end)

