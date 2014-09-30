---
layout: default
categories: blog
tags: ruby sidekiq queue
title: Move sidekiq jobs from one queue to another
---

We had a problem the other day that resulted in many jobs getting put into the
default queue for sidekiq workers to process, this held up other more important
jobs.

I fixed this by telling redis to put jobs for one queue into another with
a lower (if any) priority.

I just did this is a rails console (scary cowboy style!)

    q_from = "s3uploads"
    q_to = "s3uploads_pending"
    count_block = proc{ Sidekiq.redis { |conn| conn.llen("queue:#{q_from}") } }
    while count_block.call > 0 do
      Sidekiq.redis { |conn| conn.rpoplpush "queue:#{q_from}", "queue:#{q_to}" }
    end
