- publish data to SNS topic
- SQS Queues subscribe to Topic
- full decoupled
- ability to add more SQS over time
- SQS Access policy must allow of SNS write
- can have FIFO SNS Topics
    - similar features as SQS ⇒ Ordering, deduplication
    - can only have SQS FIFO queues as subscribers

Useful for S3:

Can only have one event rule per prefix event type combo ⇒ we can send the events to many SQS queues using this pattern