---
layout: default
categories: blog
tags: linux ruby
title: Things I should already know about Ruby
---

I have been listening to a few talks from Aloha Ruby Conf, and found the 
following really informative:

[James Edard Gray II - Talk](http://www.youtube.com/watch?v=aBgnlBoIkVM)

At this point I have not listen the whole thing, but so far it is has been
a lesson in how little I know.

Below is a script that demos some of things James talked about, run it like so (if you named it demo.out):

    ruby -x demo.out

Script here:

    Hello,
    
    This is a run down of the tips from the Aloha Ruby Conf
    You can see the ruby embedded in this file.
    
    #!/usr/bin/env ruby
    
    # programmatically turn on warnings
    $VERBOSE = true
    
    # set this before any 'require' lines to get a hash of files and lines
    # dynamically loaded
    SCRIPT_LINES__ = { }
    
    require "logger"
    
    puts "Tested this shizzle ... and it worked"
    
    # DATA io handle is created when Ruby sees "__END__"
    # You can play with it
    DATA.rewind
    DATA.each do |l|
      puts "   Line from a rewind: #{l}"
    end
    
    # Here we use DATA to get a lock on the file, so it is only run once!
    DATA.flock(File::LOCK_EX | File::LOCK_NB) or abort "Already running"
    trap("INT", "EXIT")
    puts "runnning..."
    2.times do |i|
      sleep 1;
      puts "  looping #{i}"
    end
    
    if ( SCRIPT_LINES__ ) then
      puts "This is dynamically loaded code: #{SCRIPT_LINES__.inspect}"
    end
    
    
    puts " We can run ruby with the following options:
    
      --dump parsetree
      --dump parsetree_with_comment
      --dump insns
      --dump yydebug
    
    "
    
    #Autovivication
    deep = Hash.new { |hash, key| hash[key] = Hash.new(&hash.default_proc) }
    deep[:a][:b][:c]
    puts "Autovivication: #{deep}"
    
    str = "Prices: $127.12 $12.34 - They are cheap!"
    puts str[/\$(?<dollars>\d+)/, :dollars]
    puts str[/\w+?/]
    
    # sprintf type formatting in 1.9
    def show_off(name, content)
      puts " %s : %p " % [name, content];
    end
    show_off "PercentP does .inspect", {"grays" => %w[Jim Joe Ben] }
    
    #puts "%<item>-%<item_size>s | %<price>#{price_size}s" % { item: "Item", price: "Price"}

    # Tips:
    puts "Hash  - Use .fetch     .. it has better warnings and you can give it default value/block"
    puts "Array - Use .zip       .. you can move through multiple arrays in lock step"
    puts "Array - Use .partition .. you can split up an array with a block that runs a yes/no"
    puts "Array - Use .flat_map  .. if the values are Array's, it flattens then and you just see the all values"
    
    __END__

