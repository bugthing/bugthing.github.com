---
layout: default
categories: blog
tags: ruby rails repl bin
title: A REPL a Rails app
---

I wanted a tool to do little tricks within MyApp, they were abit beond rake tasks, so I thought
i'd give writting a custom REPL a do.

Below is boiler plate for `./bin/repl.rb`, and can you started

    #!/usr/bin/env ruby
    
    require File.expand_path('../../config/boot',  __FILE__)
    require File.expand_path('../../config/environment',  __FILE__)
    
    puts <<REPLHEADER
      My REPL - Interface to do stuff within My App
    
        This little tool was written to help devs do things within the system
        without having to collect knowledege from all over with some XML
        and push/pull data in/out of the system.
    
        Commands:
          t        - tenant. diplay the current tenant.
          ts(STR)  - tenant switch. switch the current tenant.
    
    REPLHEADER
    
    class MyRepl
    
      def initialize
        repl = -> prompt do
          print prompt
          handle_input(gets.chomp!)
        end
    
        loop do
          repl[">> "]
        end
      end
    
      def handle_input(input)
        result = eval(input)
        puts(" => #{result}")
      end
    
      private
    
      def t
        puts ::Apartment::Tenant.current_tenant
      end
    
      def ts(tenant_to_switch_2)
        ::Apartment::Tenant.switch tenant_to_switch_2
        puts "Switched to Tenant: #{tenant_to_switch_2}"
      end
    end
    
    MyRepl.new
