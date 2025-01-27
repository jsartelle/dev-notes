---
{"dg-publish":true,"dg-path":"Message queues.md","permalink":"/message-queues/","tags":["tech/networking"]}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://www.baeldung.com/cs/message-queues" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://www.baeldung.com/wp-content/uploads/sites/4/2022/12/CS-Featured-Image-10.jpg')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Introduction to Message Queues | Baeldung on Computer Science</h1>
		<p class="rich-link-card-description">
		Learn how to use message queues for efficient communication.
		</p>
		<p class="rich-link-href">
		https://www.baeldung.com/cs/message-queues
		</p>
	</div>
</a></div>

# Basics

- a queue that holds messages to be processed **asynchronously**, but **sequentially**
- example uses:
    - sending data from one service to another to be processed
    - distributing tasks across multiple servers
    - processing orders and payments on an online store
    - processing bank transactions
- messages can be held even when the services consuming them are offline or busy
- messages are in a standardized format, so it doesn’t matter what language or platform the connected services run on
- *producer*/*sender*/*publisher*: a service that creates messages and puts them in the queue
- *consumer*/*receiver*/*subscriber*: a service that takes messages from the queue and processes them
- *topic*: a way to group messages, so that consumers can subscribe to only the subset of messages that are relevant to them
- *message broker*: the service that manages the message queue
- *dead letter queue* (DLQ) or *dead letter exchange* (DLX): a queue which holds messages that have expired or failed to process, letting them be inspected before moving back into a source queue

# Publisher-Subscriber (pub-sub) model

- allows communication between multiple publishers and multiple subscribers using topics
- loosely coupled - publishers don't have to know anything about the subscribers, and vice versa

![publisher-subscriber model](https://www.baeldung.com/wp-content/uploads/sites/4/2023/08/pub_sub_model.png)

# Examples

## RabbitMQ

- simple linear queue
- *push*-based - messages are pushed to consumers (but a limit can be configured)
- "smart broker/dumb consumer" - the broker pushes messages to consumers, at which point they are removed from the queue
- very fast and reliable, can be distributed across clusters of nodes with fault tolerance
- supports standardized protocols like AMQP, MQTT, and STOMP

## Kafka

- uses the [[Development/Message queues#Publisher-Subscriber (pub-sub) model\|pub-sub]] model (topic-based)
- *pull*-based - consumers pull messages from queues in batches
- "dumb broker/smart consumer" - the broker doesn't track which messages are read, all messages are kept for a certain period of time (so Kafka can act as a log)
