### create topic
    gcloud pubsub topics create sandiego

### create subscription
    gcloud pubsub subscriptions create --topic sandiego mySub1

### publish message
    gcloud pubsub topics publish sandiego --message "hello again"

### pull message
    gcloud pubsub subscriptions pull --auto-ack mySub1

### delete subscription
    gcloud pubsub subscriptions delete mySub1
