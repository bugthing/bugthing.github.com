---
layout: default
categories: blog
tags: linux postgres
title: CSV streight out of Postgres
---

Postgres is my favorite SQL database, fast, advanced, GPL, whats not to love!?

Often my boss wants some data out our db that he can consume from within Numbers on his Mac, so
CSV is the way to go and I often forget the syntax, but this is roughly what I do:

    psql -h 127.0.0.1 -U USER DB_NAME -F --no-align -c "COPY (SELECT * FROM mytable WHERE something IS NULL AND something_else=5) TO '/tmp/fortheboss.csv' WITH CSV HEADER"


