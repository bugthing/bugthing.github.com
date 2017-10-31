---
layout: default
title: average-time-per-line-write
---
Someone asked me "How long are those background jobs taking?"

Seemingly simple question right!?.. well it involved figuring out when a job started
and when if finished then calculating an average, problem is  I am well lazy and dont
want to do all that.

I know that all jobs create a single log line in a proxy log, so figured I could just find
the rate at which lines are being added to a file.. so a googled and found this:

    tail -n 0 -f /var/log/proxy/access.json | pv -l -i10 -r >/dev/null

thanks lazyweb

