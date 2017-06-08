---
layout: default
categories: blog
tags: database mysql bash
title: Run SQL for every DB in MySQL
---

Unfortunately I have to use MySQL at $work, we have a tonne of databases (multi-tenant app), to track down problems I somethings
need to run some SQL on every DB .. heres a some bash I threw together to run some SQL on each:

```
#!/bin/bash

# Connection deetz:
MUSER="root"
MPASS="password"
MHOST="0.0.0.0"

# SQL to run on each db:
SQL="SELECT e.person_id FROM employees e JOIN contacts c ON e.person_id=c.person_id;"

#------ the meat on this script: ------#

MYSQL="$(which mysql)"

DBS="$(${MYSQL} -u ${MUSER} -h $MHOST -p${MPASS} -Bse 'show databases')"

for db in $DBS; do

  # echo "Running SQL on ${db}"

  RESULT="$(echo "${SQL}" | ${MYSQL} -u ${MUSER} -h ${MHOST} -p${MPASS} ${db} 2> /dev/null)"

  if [ $? -gt 0 ]; then
    # none 0 error code .. perhaps the sql failed on the db? .. noop
    :
  else
    if [[ "$RESULT" = "" ]]; then
      # SQL returned nothing
      :
    else
      echo "Found result in ${db}"  #: ${RESULT}"
    fi
  fi

done
```

