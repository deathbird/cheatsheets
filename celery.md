# Celery

## What’s a Task Queue?
Task queues are used as a mechanism to distribute work across threads or machines.

A task queue’s input is a unit of work called **a task**. Dedicated worker processes constantly monitor task queues for new work to perform.

Celery communicates via **messages**, usually using a **broker** to mediate between **clients** and **workers**. To initiate a task the client adds a message to the queue, the broker then delivers that message to a worker.

Celery requires a message transport to send and receive messages. The RabbitMQ and Redis broker transports are feature complete. If you want to keep track of the tasks’ states, Celery needs to store or send the states somewhere. There are several built-in result backends like Redis.

## Tasks
> When you send a task message in Celery, that message won’t contain any source code, but only the name of the task you want to execute. This works similarly to how host names work on the internet: every worker maintains a mapping of task names to their actual functions, called the task registry.

Tasks are the building blocks of Celery applications. A task is a class that can be created out of any callable via the `@app.task` or the `@shared_task` decorator (the latter for Django). It performs dual roles in that it defines both what happens when a task is called (sends a message), and what happens when a worker receives that message.

Every task class has a unique name, and this name is referenced in messages so the worker can find the right function to execute.

A task message is not removed from the queue until that message has been acknowledged by a worker. A worker can reserve many messages in advance and even if the worker is killed – by power failure or some other reason – the message will be redelivered to another worker. (Giannos: possibly workers reserve messages and if they crash these messages are available to be reserverd by other workers).

> Ideally task functions should be idempotent: meaning the function won’t cause unintended effects even if called multiple times with the same arguments.

Since the worker cannot detect if your tasks are idempotent, the default behavior is to acknowledge the message in advance, just before it’s executed, so that a task invocation that already started is never executed again.

If your task is idempotent you can set the acks_late option to have the worker acknowledge the message after the task returns instead. See also the FAQ entry (below) "Should I use retry or acks_late?".

Note that the worker will acknowledge the message if the child process executing the task is terminated (either by the task calling sys.exit(), or by signal - Giannos: the worker crashes) even when acks_late is enabled. This behavior is intentional.

A task being **bound** means the first argument to the task will always be the task instance (self), just like Python bound methods:
```
@app.task(bind=True)
def add(self, x, y):
    logger.info(self.request.id)
```
Bound tasks are needed for retries (using self.retry()), for accessing information about the current task request, and for any additional functionality you add to custom task base classes.

* Every task must have a unique name. A best practice is to use the module name as a name-space, this way names won’t collide if there’s already a task with that name defined in another module.

* **app.Task.request** contains information and state related to the currently executing task.

### Task retries
https://docs.celeryq.dev/en/stable/userguide/tasks.html#retrying

## Calling tasks (API)
1. Star method: `add.delay(2, 2)`. This method is a star-argument shortcut of the more feature rich...
2. `add.apply_async((2, 2), queue='slow', countdown=10)` this method enables using execution options (kwargs).
3. Finally applying the task directly will execute the task in the current process, so that no message is sent `add(2,2)`.

Every task invocation will be given a unique identifier (an UUID) – this is the task id. The delay and apply_async methods return an AsyncResult instance, which can be used to keep track of the tasks execution state. But for this you need to enable a result backend so that the state can be stored somewhere.

A task can only be in a single state, but it can progress through several states. The stages of a typical task can be:

```PENDING -> STARTED -> SUCCESS```

The started state is a special state that’s only recorded if the **task_track_started** setting is enabled, or if the @task(track_started=True) option is set for the task. The pending state is actually not a recorded state, but rather the default state for any task id that’s unknown.
If the task is retried the stages can become even more complex. To demonstrate, for a task that’s retried two times the stages would be:

```PENDING -> STARTED -> RETRY -> STARTED -> RETRY -> STARTED -> SUCCESS```

### Signatures
Sometimes you may want to pass the signature of a task invocation to another process or as an argument to another function, for which Celery uses something called **signatures**. A signature wraps the arguments and execution options of a single task invocation in such a way that it can be passed to functions or even serialized and sent across the wire!

* You can create a signature for the add task using the arguments (2, 2), and a countdown of 10 seconds like this: `add.signature((2, 2), countdown=10)`
* There’s also a shortcut using star arguments: `add.s(2, 2)`

> Signature instances also support the calling API, meaning they have `delay ` and `apply_async ` methods.

But there’s a difference in that the signature may already have an argument signature specified. The add task takes two arguments, so a signature specifying two arguments would make a complete signature:
```
s1 = add.s(2, 2)
res = s1.delay()
```
But, you can also make incomplete signatures to create what we call partials:
```
# incomplete partial: add(?, 2)
s2 = add.s(2)
```
s2 is now a partial signature that needs another argument to be complete, and this can be resolved when calling the signature:
```
# resolves the partial: add(8, 2)
res = s2.delay(8)
```
Here you added the argument 8 that was prepended to the existing argument 2 forming a complete signature of add(8, 2).

Keyword arguments can also be added later; these are then merged with any existing keyword arguments, but with new arguments taking precedence:
```
s3 = add.s(2, 2, debug=True)
s3.delay(debug=False)   # debug is now False.
```
As stated, signatures support the calling API: meaning that:

* `sig.apply_async(args=(), kwargs={}, **options)` Calls the signature with optional partial arguments and partial keyword arguments. Also supports partial execution options.

* `sig.delay(*args, **kwargs)` Star argument version of apply_async. Any arguments will be prepended to the arguments in the signature, and keyword arguments is merged with any existing keys.

> Using signatures enables us to create complex task workflows using celery canvas primitives which themselves are also signatures!

## Performance and Strategies [link](https://docs.celeryq.dev/en/stable/userguide/tasks.html#performance-and-strategies)
* Granularity: The task granularity is the amount of computation needed by each subtask. In general it is better to split the problem up into many small tasks rather than have a few long running tasks. With smaller tasks you can process more tasks in parallel and the tasks won’t run long enough to block the worker from processing other waiting tasks. However, ... see link above.
* State: Since Celery is a distributed system, you can’t know which process, or on what machine the task will be executed. You can’t even know if the task will run in a timely manner. The ancient async sayings tells us that “asserting the world is the responsibility of the task”. What this means is that the world view may have changed since the task was requested, so the task is responsible for making sure the world is how it should be... see link above.
* DB transactions: use the on_commit callback to launch your Celery task once all transactions have been committed successfully.

## Notes

* Concurrency: The default concurrency number is the number of CPU’s on that machine (including cores). Experimentation has shown that adding more than twice the number of CPU’s is rarely effective, and likely to degrade performance instead.
* Results are disabled by default because there is no result backend that suits every application. For many tasks keeping the return value isn’t even very useful, so it’s a sensible default to have. Also note that result backends aren’t used for monitoring tasks and workers: for that Celery uses dedicated event messages.
* **Ignore results you don’t want**. If you don’t care about the results of a task, be sure to set the ignore_result option, as storing results wastes time and resources.
```
@app.task(ignore_result=True)
def mytask():
    something()
```
* **Avoid launching synchronous subtasks**. Having a task wait for the result of another task is really inefficient, and may even cause a deadlock if the worker pool is exhausted. Make your design asynchronous instead, for example by using callbacks or chains.

### Should I use retry or acks_late?
Answer: Depends. It’s not necessarily one or the other, you may want to use both.

Task.retry is used to retry tasks, notably for expected errors that is catch-able with the try block. The AMQP transaction isn’t used for these errors: if the task raises an exception it’s still acknowledged!

The acks_late setting would be used when you need the task to be executed again if the worker (for some reason) crashes mid-execution. It’s important to note that the worker isn’t known to crash, and if it does it’s usually an unrecoverable error that requires human intervention (bug in the worker, or task code).

In an ideal world you could safely retry any task that’s failed, but this is rarely the case. Imagine the following task:
```
@app.task
def process_upload(filename, tmpfile):
    # Increment a file count stored in a database
    increment_file_counter()
    add_file_metadata_to_db(filename, tmpfile)
    copy_file_to_destination(filename, tmpfile)
```
If this crashed in the middle of copying the file to its destination the world would contain incomplete state. This isn’t a critical scenario of course, but you can probably imagine something far more sinister. So for ease of programming we have less reliability; It’s a good default, users who require it and know what they are doing can still enable acks_late (and in the future hopefully use manual acknowledgment).

In addition Task.retry has features not available in AMQP transactions: delay between retries, max retries, etc.

>So use retry for Python errors, and if your task is idempotent combine that with acks_late if that level of reliability is required.