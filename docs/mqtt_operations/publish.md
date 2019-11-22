---
layout: default
title: Publish
parent: MQTT Operations
nav_order: 2
---

# Publish

Messages are published with a topic.
The MQTT broker needs the topic to route the message to subscribers.
Hence the topic is mandatory for a Publish message (it is the only required property).

{% capture tab_content %}

MQTT 5.0
===

 {% capture tab_content %}

 Blocking
 ===

The blocking API directly returns a `Mqtt5PublishResult` if publishing was successful.

```java
Mqtt5PublishResult publishResult = client.publishWith().topic("test/topic").send();
```

 ====

 Async
 ===

The asynchronous API returns a `CompletableFuture` which completes with a `Mqtt5PublishResult` message if publishing 
was successful.

```java
CompletableFuture<Mqtt5PublishResult> publishResultFuture = 
        client.publishWith().topic("test/topic").send();
```

 ====

 Reactive
 ===

The reactive API does not publish a single message, but an asynchronous stream of messages: a `Flowable<Mqtt5Publish>`.
Hence it also returns an asynchronous stream of results: `Flowable<Mqtt5PublishResult>`.
Each published message will cause a result in the returned stream.
As the `Flowable` is a reactive type, the following line does not publish immediately but only after you subscribe to it 
(in terms of Reactive Streams).

```java
Flowable<Mqtt5PublishResult> publishResultFlowable = client.publish(
        Flowable.range(0, 100).map(i -> Mqtt5Publish.builder().topic("test/topic" + i).build()));
```

 {% endcapture %}{% include tabs.html tab_group="api-flavour" %}

Depending on the Quality of Service (QoS) of the Publish message the result can be:

| `Mqtt5PublishResult` | QoS 0 | returned when the message is written to the transport |
| `Mqtt5Qos1Result`    | QoS 1 | returned when the message is acknowledged, contains the `Mqtt5PubAck` message |
| `Mqtt5Qos2Result`    | QoS 2 | returned when the message is acknowledged, contains the `Mqtt5PubRec` message |

 {% capture tab_content %}

 Blocking
 ===

If publishing was not successful, it throws:

| `Mqtt5PubAckException`        | for QoS 1 if the PubAck message contained an error code (the PubAck message is contained in the exception) |
| `Mqtt5PubRecException`        | for QoS 2 if the PubRec message contained an error code (the PubRec message is contained in the exception) |
| `ConnectionClosedException`   | for QoS 0 if the connection was closed during writing the message to the transport |
| `MqttSessionExpiredException` | if the session expired before the message has been acknowledged completely |
| `MqttEncodeException`         | if the maximum packet size was exceeded |
| `MqttClientStateException`    | if the client is not connected and also not reconnecting |

 ====

 Async
 ===

If publishing was not successful, the `CompletableFuture` completes exceptionally with:

| `Mqtt5PubAckException`        | for QoS 1 if the PubAck message contained an error code (the PubAck message is contained in the exception) |
| `Mqtt5PubRecException`        | for QoS 2 if the PubRec message contained an error code (the PubRec message is contained in the exception) |
| `ConnectionClosedException`   | for QoS 0 if the connection was closed during writing the message to the transport |
| `MqttSessionExpiredException` | if the session expired before the message has been acknowledged completely |
| `MqttEncodeException`         | if the maximum packet size was exceeded |
| `MqttClientStateException`    | if the client is not connected and also not reconnecting |

 ====

 Reactive
 ===

If publishing a single message was not successful, the corresponding publish result contains an error:

| `Mqtt5PubAckException`        | for QoS 1 if the PubAck message contained an error code (the PubAck message is contained in the exception) |
| `Mqtt5PubRecException`        | for QoS 2 if the PubRec message contained an error code (the PubRec message is contained in the exception) |
| `ConnectionClosedException`   | for QoS 0 if the connection was closed during writing the message to the transport |
| `MqttSessionExpiredException` | if the session expired before the message has been acknowledged completely |
| `MqttEncodeException`         | if the maximum packet size was exceeded |

The result stream will always emit a publish result for every message in the input stream.
This means that the result stream only errors completely if the provided message stream errors itself and after all 
publish results have been received.
Similarly, the result stream only completes normally if the provided message stream completes normally itself and after 
all publish results have been received.

 {% endcapture %}{% include tabs.html tab_group="api-flavour" tab_no_header=true %}

====


MQTT 3.1.1
===

 {% capture tab_content %}

 Blocking
 ===

The blocking API returns nothing if publishing was successful.

```java
client.publishWith().topic("test/topic").send();
```

If publishing was not successful, it throws:

| `ConnectionClosedException`   | for QoS 0 if the connection was closed during writing the message to the transport |
| `MqttSessionExpiredException` | if the session expired before the message has been acknowledged completely |
| `MqttEncodeException`         | if the maximum packet size was exceeded |
| `MqttClientStateException`    | if the client is not connected and also not reconnecting |

 ====

 Async
 ===

The asynchronous API returns a `CompletableFuture` which completes with the `Mqtt3Publish` message (as context) if 
publishing was successful.

```java
CompletableFuture<Mqtt3Publish> publishResultFuture = 
        client.publishWith().topic("test/topic").send();
```

If publishing was not successful, the `CompletableFuture` completes exceptionally with:

| `ConnectionClosedException`   | for QoS 0 if the connection was closed during writing the message to the transport |
| `MqttSessionExpiredException` | if the session expired before the message has been acknowledged completely |
| `MqttEncodeException`         | if the maximum packet size was exceeded |
| `MqttClientStateException`    | if the client is not connected and also not reconnecting |

 ====

 Reactive
 ===

The reactive API does not publish a single message, but an asynchronous stream of messages: a `Flowable<Mqtt3Publish>`.
Hence it also returns an asynchronous stream of results: `Flowable<Mqtt3PublishResult>`.
Each published message will cause a result in the returned stream.
As the `Flowable` is a reactive type, the following line does not publish immediately but only after you subscribe to it 
(in terms of Reactive Streams).

```java
Flowable<Mqtt3PublishResult> publishResultFlowable = client.publish(
        Flowable.range(0, 100).map(i -> Mqtt3Publish.builder().topic("test/topic" + i).build()));
```

If publishing a single message was not successful, the corresponding publish result contains an error:

| `ConnectionClosedException`   | for QoS 0 if the connection was closed during writing the message to the transport |
| `MqttSessionExpiredException` | if the session expired before the message has been acknowledged completely |
| `MqttEncodeException`         | if the maximum packet size was exceeded |

The result stream will always emit a publish result for every message in the input stream.
This means that the result stream only errors completely if the provided message stream errors itself and after all 
publish results have been received.
Similarly, the result stream only completes normally if the provided message stream completes normally itself and after 
all publish results have been received.

 {% endcapture %}{% include tabs.html tab_group="api-flavour" %}

{% endcapture %}{% include tabs.html tab_group="mqtt-version" %}

***



{% capture tab_content %}

MQTT 5.0
===

The rest of this section describes all possible properties of a `Mqtt5Publish` message.
They can be set via a fluent builder API.

 {% capture tab_content %}

 Blocking
 ===

  {% capture tab_content %}

  Fluent
  ===

```java
Mqtt5PublishResult publishResult = client.publishWith()
        ... // here you can specify multiple properties which are described below
        .send();
```

  ====

  Prebuilt message
  ===

```java
Mqtt5Publish publishMessage = Mqtt5Publish.builder()
        ... // here you can specify multiple properties which are described below
        .build();

Mqtt5PublishResult publishResult = client.publish(publishMessage);
```

  {% endcapture %}{% include tabs.html tab_group="mqtt-operation-style" %}

 ====

 Async
 ===

  {% capture tab_content %}

  Fluent
  ===

```java
CompletableFuture<Mqtt5PublishResult> publishResultFuture = client.publishWith()
        ... // here you can specify multiple properties described below
        .send();
```

  ====

  Prebuilt message
  ===

```java
Mqtt5Publish publishMessage = Mqtt5Publish.builder()
        ... // here you can specify multiple properties described below
        .build();

CompletableFuture<Mqtt5PublishResult> publishResultFuture = client.publish(publishMessage);
```

  {% endcapture %}{% include tabs.html tab_group="mqtt-operation-style" %}

 ====

 Reactive
 ===

  {% capture tab_content %}
  -===
  {% endcapture %}{% include tabs.html tab_group="dummy" %}

```java
Flowable<Mqtt5PublishResult> publishResultFlowable = client.publish(
        Flowable.range(0, 100).map(i -> 
                Mqtt5Publish.builder()
                        ... // here you can specify multiple properties described below
                        .build()
        ));
```

 {% endcapture %}{% include tabs.html tab_group="api-flavour" tab_merge=true %}

====


MQTT 3.1.1
===

The rest of this section describes all possible properties of a `Mqtt3Publish` message.
They can be set via a fluent builder API.

 {% capture tab_content %}

 Blocking
 ===

  {% capture tab_content %}

  Fluent
  ===

```java
client.publishWith()
        ... // here you can specify multiple properties which are described below
        .send();
```

  ====

  Prebuilt message
  ===

```java
Mqtt3Publish publishMessage = Mqtt3Publish.builder()
        ... // here you can specify multiple properties which are described below
        .build();

Mqtt3PublishResult publishResult = client.publish(publishMessage);
```

  {% endcapture %}{% include tabs.html tab_group="mqtt-operation-style" %}

 ====

 Async
 ===

  {% capture tab_content %}

  Fluent
  ===

```java
CompletableFuture<Mqtt3Publish> publishResultFuture = client.publishWith()
        ... // here you can specify multiple properties described below
        .send();
```

  ====

  Prebuilt message
  ===

```java
Mqtt3Publish publishMessage = Mqtt5PuMqtt3Publishblish.builder()
        ... // here you can specify multiple properties described below
        .build();

CompletableFuture<Mqtt3Publish> publishResultFuture = client.publish(publishMessage);
```

  {% endcapture %}{% include tabs.html tab_group="mqtt-operation-style" %}

 ====

 Reactive
 ===

  {% capture tab_content %}
  -===
  {% endcapture %}{% include tabs.html tab_group="dummy" %}

```java
Flowable<Mqtt3PublishResult> publishResultFlowable = client.publish(
        Flowable.range(0, 100).map(i -> 
                Mqtt3Publish.builder()
                        ... // here you can specify multiple properties described below
                        .build()
        ));
```

 {% endcapture %}{% include tabs.html tab_group="api-flavour" tab_merge=true %}

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}



{% capture tab_content %}

MQTT 5.0
===

- [Topic](#topic)
- [Payload](#payload)
- [Quality of Service (QoS)](#quality-of-service-qos)
- [Retain](#retain)
- [Message Expiry Interval](#message-expiry-interval)
- [Payload Format Indicator](#payload-format-indicator)
- [Content Type](#content-type)
- [Response Topic](#response-topic)
- [Correlation Data](#correlation-data)
- [User Properties](#user-properties)

====

MQTT 3.1.1
===

- [Topic](#topic)
- [Payload](#payload)
- [Quality of Servive (QoS)](#quality-of-service-qos)
- [Retain](#retain)

{% endcapture %}{% include tabs.html tab_group="mqtt-version" %}

***


## Topic

{% capture tab_content %}

MQTT 5.0
===

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}

***



## Payload

{% capture tab_content %}

MQTT 5.0
===

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}

***



## Quality of Service (QoS)

{% capture tab_content %}

MQTT 5.0
===

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}

***



## Retain

{% capture tab_content %}

MQTT 5.0
===

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}




{% capture tab_content %}

MQTT 5.0
===

***

## Message Expiry Interval

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}




{% capture tab_content %}

MQTT 5.0
===

***

## Payload Format Indicator

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}




{% capture tab_content %}

MQTT 5.0
===

***

## Content Type

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}




{% capture tab_content %}

MQTT 5.0
===

***

## Response Topic

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}




{% capture tab_content %}

MQTT 5.0
===

***

## Correlation Data

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}




{% capture tab_content %}

MQTT 5.0
===

***

## User Properties

====

MQTT 3.1.1
===

{% endcapture %}{% include tabs.html tab_group="mqtt-version" tab_no_header=true %}