---
layout: default
title: list-all-open-ports
---
List all open ports

I always google this! .. so to save time, I write it down here:

```
netstat --tcp --listening --programs --numeric
```

.. or, the same with less characters..

```
netstat -lptn
```


