
- use to decouple applications

Producer - Send messages to the Queue

Consumer - Poll messages from the Queue


# Producer

- messages produced with the SDK (`SendMessage`)

# Consumer

- can receive up to 10 messages at a time
- run on EC2, on-prem, AWS Lambda

Multiple Consumers

- receive and process messages in parallel
- if we need more throughput, we can add more consumers

# SQS with ASG


- set up a CloudWatch metric to check for Queue Length via `ApproximateNumberOfMessages`
- ClouthWatch alarm to watch metric, when triggered ⇒ scale the ASG

# Standard Queue

- unlimited throughput, unlimited # of messages in queue
- retention: default = 4 days, maximum = 14 days
- low latency (<10ms)
- 256KB per message sent
- at-least once delivery
- best effort ordering

  

# SQS Security

- Encryption
    - Inflight ⇒ HTTPS
    - at-rest ⇒ KMS
    - client-side ⇒ not support by SQS out of the box
- Access control ⇒ IAM policy to regulate access to SQS API
- SQS Access Policy
    - useful for cross-account queue
    - allows other services (S3) to write to queue

## SQS Access Policy

- similar to S3 policy
- JSON IAM policies (resource Policy)

Use Cases:

Cross Account

- Principal (EC2 Instance)⇒ policy is attached to queue (1111222233333)
- Action ⇒ receive messages from
- Resource (SQS Queue) ⇒ 444455556666


Public S3 events to SQS Queue


# Visibility Timeout

- Messages become invisible to other consumers when it is polled by a consumer. After the invisibility timeout, if the message is processed, it is added back to the queue.
- Default timeout = 30 seconds
- if the consumer needs longer than 30 seconds ⇒ use the `ChangeMessageVisibility` API to get more time ⇒ o.w. the message could be processed twice

# Dead Letter Queue

- set a threshold for how many times a message can be added back into the queue after the consumer fails to process the message within the invisibility Timeout
- Threshold is called `MaximumReceives`
- Messages are added to the Dead Letter Queue (DLQ)

## Redrive to Source

- feature to help consume messages from DLQ to find out what is wrong with them
- when code is fixed ⇒ add message back into source queue (or other queue)

# Delay Queue

- Delay messages up to 15 minutes so that consumers don’t see them
- default is 0 seconds
- can set default at the queue level
- or can see delay for each message using `DelaySeconds` param

  

# Developer Level Concepts

## Long Polling

- consumer can wait for messages if there are none in the queue
- increases efficiency ⇒ decreases API calls to the SQS
- decreases latency ⇒ as soon as message arrives, it is consumed
- set between 1 to 20 seconds
- can be set at the Queue levele
- can be set at the API level using the `WaitTimeSeconds` param

## Extended Clients


- Java library
- allows us to “send” messages larger than 256KB
- producer adds large message to S3 ⇒ then sends metadata message to SQS
- consumer polls the small metadata message and reads where the data is from S3
- consumer gets large message from S3

## API calls

CreateQueue (add MessageRetention param)⇒ create a queue

DeleteQueue ⇒ delete a queue

PurgeQueue ⇒ delete all messages in queue

SendMessage (add DelaySeconds param) ⇒

ReceiveMessages ⇒ do polling

MaxNumberOfMessages default = 1, max = 10

DeleteMessages ⇒ delete message once processed by consumer

ReceiveMessageWaitTimeSeconds: long polling

ChangeMessageVisibility ⇒ change message timeout

* Batch APIs for SendMessage, DeleteMessage, ChangeMessageVisibility


# FIFO Queues

- messages ordered in the queue
- first in first out
- 300 msgs/s without batching
- 3000 msgs/s with batching
- guaranteed ordering and Exactly once send capability

## Deduplication

- if you send the same message twice within an interval, the second one will be refused
- interval is 5 minutes
- Two types:
    - Content-Based: SHA-256 hash of body ⇒ if same hash encountered twice ⇒ second one will be refused
    - Message Deduplication ID ⇒ if same ID is ecnountered twice, the second won’t be send

## Message Grouping

- same value for MessageGroupID ⇒ you can only have one consumer and all messages are in order
- for ordering at a subset of the messages ⇒ use different groupID
    - messages with the same groupID will be sent to the same consumer ⇒ parallel processing
    - ordering across groups is not guaranteed
