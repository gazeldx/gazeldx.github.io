---
layout: post
title:  "Amazon Elastic Beanstalk Deploy Docker App"
date:   2020-06-05 21:11:11
categories: aws
---

# EB
https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-getting-started.html

* Amazon Machine Image (AMI) is actually a centOS Linux.


`eb init`
* Please select platform: Choose Docker.
* Branch: Choose 64bit Amazon Linux

`eb create`
* Load balance type: Choose 1) Classic

`eb ssh`
* For `ERROR: NotFoundError - The EB CLI cannot find your SSH key file for keyname "something-aws-eb".`
You need to ask someone to get the `something-aws-eb` file and put it into `~/.ssh`.

`eb status`

`eb health`

`eb events`

https://docs.aws.amazon.com/elasticbeanstalk/latest/platforms/platforms-supported.html#platforms-supported.docker

For us, we use `64bit Amazon Linux` 2018.03 v2.20.2 (Not `64bit Amazon Linux 2`).

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb3-cli-git.html

* You can deploy any branch.
* You can even deploy uncommitted files.

# Docker
```shell script
eb ssh
# After you have logged in AWS EC, you can  
sudo docker ps
sudo docker exec -it the_container_id_or_name bash
```

# RDS
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html

After you have created the database, you should be able to connect to newly created RDS.

If you can't, it is probably because of the `Security Group`.

For PostgreSQL, please create a `Security Group` with a Inbound 5432 port, then apply this `Security Group` to your RDS.    
