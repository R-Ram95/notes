- allows us to send one message to many receivers
- pub/sub
- publish messages to an SNS topic, receiver subscribe to the topic
- 12.5M subscribers per topic
- 100K topics limit
- same security as SQS ⇒ has SNS policies as well

# How to Publish

Topic Publish (using SDK)

- create topic
- create subscription
- public to topic

Direct Public (mobile apps SDK)

- create platform application
- create platform endpoint
- publish to platform endpoint