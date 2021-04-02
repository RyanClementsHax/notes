# Messaging

## Prefetch
- messaging libraries like RabbitMQ have a prefetch option that will configure how many messages it grabs
- if your prefetch is too big, the messages you acquire if processing them brings in large files can lead to out of memory (OOM) errors

## High performance queues
- if you can tolerate message loss, using redis as a queue can function well (bbc.com uses this pattern at a massive scale)
