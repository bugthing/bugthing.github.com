---
layout: default
title: try-graphile
---

I saw a post about (graphile)[https://www.graphile.org/] and thought it sounded like
a great way to take a DB and use via a javascript client very easily.

To get started I did this:

1. Spin up a postgres database server

    $ docker run -d --name postgres -p 5432:5432 -v /opt/docker-volumes/postgres/data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=postgres postgres

  create a database

    $ docker run -it --link postgres postgres createdb --host postgres -U postgres try_stuff

2. Import some example data (if you've got no data)_

    $ wget "http://cl.ly/173L141n3402/download/example.dump"
    $ docker run -it -v `pwd`:/tmp/x --link postgres postgres pg_restore --host postgres -U postgres --dbname try_stuff /tmp/x/example.dump

3. Start the Graphile server

    $ npm install -g postgraphile
    $ npx postgraphile -c postgres://postgres:postgres@0.0.0.0/try_stuff --schema public

4. Visit the graphiql endpoint in a browser and play about

    http://localhost:5000/graphiql

5. Use the api endpoint as you would any GraphQL service

    http://localhost:5000/graphql

