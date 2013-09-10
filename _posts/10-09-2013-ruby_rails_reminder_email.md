---
layout: default
categories: blog
tags: ruby rails
title: Reminder emails from Rails with SideKiq
---

Pre-text
--------

Imagine you have an Rails app, that already uses SideKiq to queue the sending of emails. You need
to add to this app and create a Rails model to represent a room booking.

At different stages of the booking you should do different things.

*  When a booking is in 'provisioned', send a email to warning the room owner

*  When a booking is 'confirmed', informed the booker their booking is confirmed.

*  A booking can be altered before it becomes 'live'

*  2 hours before the start time of the booking send the booker a reminder email.

Ruby
----

To help with th different states of a RoomBooking I have implemented the workflow gem, so workflow for RoomBooking, might look like:

    workflow do
      state :draft do
        event :provision, :transitions_to => :provisional
      end
      state :provisional do
        event :confirm, :transitions_to => :confirmed
        on_entry do
          RoomBookingMailer.delay.inform_owner_of_booking(self.id)
        end
      end
      state :confirmed do
        event :activate, :transitions_to => :active
        on_entry do
          RoomBookingMailer.delay.inform_booker_of_booking_confirmation(self.id)
        end
      end
      state :active do
        event :complete, :transitions_to => :complete
      end
      state :complete do
      end
    end

My Problem
----------

What about the reminder??

I could schedule a task that checks upcoming bookings and sends out reminders when required.
The thing I do not like about this is:

*  I need to record I have sent out the reminder

*  Process is defined outside of the RoomBooking

I could do:

    RoomBookingMailer.delay_until( booking.start - 2.hours ).remind_booker_of_booking(self.id)

But what if they change their mind and alter the booking start time or cancel it, I would have to un-queue
the reminder email and possibly queue another (messy!)

So I did this:

    RoomBookingReminderWorker.perform_in(2.minutes, self.id, :customer_confirmed)

Then in that worker will (in 2 minutes) do the following:

*  Checks to see if the booking is still valid

*  Checks to see if it is time to send the reminder

If the booking it still valid and it is NOT time to send the reminder, it will simply schedule itself to run again in X amount of time.

