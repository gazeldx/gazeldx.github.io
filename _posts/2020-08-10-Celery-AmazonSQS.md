---
layout: post
title:  "Celery AmazonSQS"
date:   2020-08-10 21:11:11
categories: Python
---

# Celery
These two issue are questions of mine:

1 If an exception is raised in your task, it seems that the task won't be executed again.
Because the message has been deleted.
My answer: https://docs.celeryproject.org/en/stable/faq.html#faq-acks-late-vs-retry
The default of `acks-late` is that the message is deleted once acknowledged by a worker.
https://docs.celeryproject.org/en/stable/userguide/tasks.html#Task.acks_late
But you can change this value to `True` (In django might by doing `CELERY_TASK_ACKS_LATE = True`).
Then your task may be consumed many times (The time interval of consuming will be the value of SQS `Default visibility timeout`).

# Amazon SQS
3 The `Default visibility timeout` will not be working when `acks-late` is `False` (default).

I do the experiment by adding `time.sleep(120)` and set `Default visibility timeout` to 30 seconds.
```python
@shared_task
def update_hubspot_deal(project_id):
    time.sleep(120)
    # Omit
```
Then the message will in `Messages in flight (not available to other consumers)`.
Unless you purge the queue, the message will be consumed endlessly (many many times).
Because every 30 seconds, a worker will consume this message.

# Troubleshoot
When the `qa` server is prepared and running, when you connect to Amazon SQS `qa` queue, 
remember you message can be consumed by both your `qa` server and your local machine.
