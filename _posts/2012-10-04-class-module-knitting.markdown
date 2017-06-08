---
layout: default
categories: blog
tags: ruby 
title: Class and Module Knitting
---

While adding a new feature to an existing rails application I was going to alter
some methods and add some new ones... I saw not too much wrong with that being 
new to rails ... but it was pointed out that over time this sort of editting results
in files consisting of large amount of unrelated code... the prefered solution
is to use 'include' and 'extend'

So take a class like this:

    class Something 
      def self.all_results
      end
      def get_result
      end
    end

If I was going to add a new method and alter how 'get_result' works I could 
just alter the class, but better than that might be to include a module.

    module SomethingNew
      def show_result
      end
      def get_result
      end
    end
    
    class Something 
      def self.all_results
      end
      def get_result
      end
      include SomethingNew
    end

This has the effect of including the new method, but the overload of the 'get_result'
does not work ... this is due to the fact the methods on the existing class also
need to be included... not fully clear on why, but this works:

    module SomethingNew
      def show_result
      end
      def get_result
      end
    end
    
    class Something 
      def self.all_results
      end
      module InstanceMethods
        def get_result
        end
      end
      include InstanceMethods
      include SomethingNew
    end

Class methods are slightly different and rather than 'include' we should use 'extend'

    class Something 
      module ClassMethods
        def all_results
        end
      end
      module InstanceMethods
        def get_result
        end
      end
      include InstanceMethods
      extend ClassMethods
      include SomethingNew
    end

If we then want to overload or add some class methods we should alter how the
module works like so

    module SomethingNew

      module ClassMethods
        def all_results
        end
        def some_results
        end
      end

      def self.included includer
        includer.extend ClassMethods
      end

      def show_result
      end

      def get_result
      end

    end

This works by calling 'included' when the module is included into another 
class, which then extends the class methods into the 'includer'

All this knitting means I can introduce new features into my rails app with only 
1 new line in my class .. and all the new code in a sperate module 

I am still waiting for all this to click in my brain but it becoming more clear.
