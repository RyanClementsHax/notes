# Parallelism

## Thread pooling

- can be dangerous for interruption because the thread that the task was originally on may have a different task on it at the time of interruption
- need to be careful with thread locals because mapping from tasks to threads isn't one to one
