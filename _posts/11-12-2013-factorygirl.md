---
layout: default
categories: blog
tags: ruby rails
title: Factory Girl
---

yo!,

Just found a well handy little trick to use when using FactoryGirl to generate model data within a Rails application my company uses.

Within out tests we do this to create a default event for 48 hours, starting in 2 hours from now:

   create :event

the above is backed with a factory defined like so:


    FactoryGirl.define do
      factory :event do
        start_at { 2.hour.from_now }
        end_at { (2 + 48).hours.from_now }
      end
    end


This served us fine for a while, but then we added some validation rules to a related model to enforce that
events could not overlap.. so doing

    create :event
    create :event

would cause a validation error as the 2 events have the same (or very simlar) start and end times.

We could address it like so

    FactoryGirl.define do
      factory :event do
        start_at { 2.hour.from_now }
        end_at { (2 + 48).hours.from_now }
      end
      factory :event_in_4_hours  do
        start_at { 4.hour.from_now }
        end_at { (4 + 48).hours.from_now }
      end
    end

Then do

    create :event
    create :event_in_4_hours

But that only solves a small problem, what if we need 3 events?
We need [Transient attributes](http://robots.thoughtbot.com/factory-girl-2-2-your-new-best-friend/)

Now we can do:

    create :event
    create :event, hours_from_now: 4
    create :event, hours_from_now: 400

And the events all start at a different time

   FactoryGirl.define do
     factory :event do

      # this block tells FactoryGirl what to arguments to ignore and applies a default:
       ignore do
         hours_from_now 2
       end

       start_at { (hours_from_now).hour.from_now }
       end_at { (hours_from_now + 48).hours.from_now }
     end
   end


Sorted!
