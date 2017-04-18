---
layout: default
categories: blog
tags: linux cli 
title: A tip for users of sudo
---
A directory is getting filled with little files containing log data, you wanna keep
it but only kind archive style... ship it to S3!

Some I wanted something quick and simple that I could set and forget, a simple bash script.

So first off to copy files from the command line i'll use the python package awscli.
To install in debian based linux and configure it, do this:

```
$ apt get install awscli
$ aws configure
```

Next Write this bash script (maybe call the file `move_logs_to_s3.sh`)

```
#!/usr/bin/env bash
#
# Moves files from a local directory to an S3 bucket.
#   - Lists the files in the local directory.
#   - Uses aws-cli to copy the file to S3 location.
#   - S3 file is: <base uri>/<date file last modified>/<filename>
#   - If the move is successful the file is deleted from local dir.
#   - Logs to STDOUT
#
# Example: ./move_logs_to_s3.sh /log s3://massive-bucket/log-bkp

DIR=${1:-"/log"}
BASES3URI=${2:-"s3://massive-bucket/log-bkp"}
DATESTART=$(date +%F)

function log {
  echo "[$(date --rfc-3339=seconds)]: $*"
}

function move_files {

  for f in `find ${DIR} -type f`
  do
    datepart=$(date +%F -r $f)
    filename=$(basename $f)
    s3uri="${BASES3URI}/$datepart/$filename"
    cmd="aws s3 cp ${f} ${s3uri}"

    log "Moving: $f to $s3uri"
    output="$(${cmd} 2>&1)"

    if [ $? -eq 0 ]; then
      log "Deleting: $f"
      rm -f $f
    else
      log "Failed: $output"
    fi

  done
}

log "Starting to move files ($DATESTART)"
move_files
echo "Finished."
```

You can now run it or if you want you could put it in a docker container and ship
it to an environment.

Wite a `Dockerfile` like so

```
FROM alpine

RUN apk --no-cache update && \
    apk --no-cache add bash python py-pip py-setuptools ca-certificates curl groff less && \
    pip --no-cache-dir install awscli 

ENV AWS_DEFAULT_REGION <REGION>
ENV AWS_ACCESS_KEY_ID <KEY ID>
ENV AWS_SECRET_ACCESS_KEY <ACCESS KEY>

ADD move_logs_to_s3.sh /usr/local/bin/move_logs_to_s3.sh
RUN chmod +x /usr/local/bin/move_logs_to_s3.sh

CMD move_logs_to_s3.sh
```

Then build and run it.

```
docker build -t little-container .
docker run -i -e AWS_DEFAULT_REGION=eu-west-1 AWS_ACCESS_KEY_ID=AAA AWS_SECRET_ACCESS_KEY=BBB little-container
```

