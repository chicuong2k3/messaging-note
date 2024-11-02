# Messaging

## Types of Messaging
- Synchronous Communication
  - HTTP
  - RPC
- Asynchronous Communication
  - Message Queues: RabbitMQ, Apache Kafka, or Amazon SQS allow messages to be sent and queued for processing at a later time by the receiver.
  - Event Streaming: Platforms like Apache Kafka enable real-time data streaming, where producers send events to a stream and consumers process those events independently.

## Advantages of Async Messaging

- Scalability: Services can scale independently based on demand, enabling better resource utilization.
- Service decouling: The producer and consumer of messages do not need to know about each other. This allows for easier updates, maintenance, and replacements of services without impacting the entire system.
- Temporal decoupling: Producers and consumers do not need to operate simultaneously.
- Fault Tolerance/More Resilience: If a service fails, messages can be stored in a queue until the service is available again, ensuring that no data is lost.
- High Availability: Enhance system availability by allowing messages to be processed even when some services are temporarily offline. 
- Load leveling: Helps distribute workloads evenly by queuing messages during peak loads. This prevents overwhelming any single service and ensures a more consistent performance.
- Integration enabled: Facilitates integration between diverse systems and applications, allowing them to communicate without requiring direct connections.

## There is no a silver bullet - The drawbacks of Async Messaging

- Complexity: Asynchronous messaging systems introduce additional complexity in architecture and design. Developers need to implement and manage message brokers, queues, and topics, which can lead to increased difficulty in monitoring and troubleshooting issues within the system
- Message Ordering: Messages may arrive out of sequence, which can lead to inconsistent states in the consuming services if they rely on a specific order of operations.
- Message Duplication: To handle this, systems must implement idempotent message processing, which can add complexity to the application logic to ensure that processing the same message multiple times does not cause unintended side effects.
- Delivery Guarantees: Ensuring message delivery guarantees ("at least once", "at most once", or "exactly once") can be difficult in asynchronous messaging systems. Each of these guarantees comes with trade-offs that can affect performance and reliability.
- Vender Lock-in: Organizations may find it difficult to migrate to another system if they heavily rely on specific features of a particular messaging service, making it challenging to change vendors or adopt more suitable solutions in the future.

## Fallacies of Distributed Computing
- The network is relible.
- Latency is 0.
- Bandwidth is infinite.
- The network is secure.
- Topology is static.
- There is one admin.
- Transport cost is 0.
- The network is homogeneous.

## Types of Coupling
- Logical: This occurs when different modules or components depend on each other based on the logic of their operations rather than on a direct reference. Changes in one module may necessitate changes in another, even if they are not directly linked.
  - Afferent Coupling (Fan In): This refers to the number of other components that depend on a specific component.
  - Efferent Coupling (Fan Out): This measures the number of components that a specific component depends on.
  - Eg:
    - After a payment is processed successfully, the Payment Gateway emits an event like PaymentProcessed.
    - The Order Processing component listens for this event and updates the order status to "Paid" without having a direct call to the Payment Gateway.
- Temporal: This type of coupling occurs when components need to be used in a specific order or within a specific time frame.
- Data: This occurs when two modules share data. The modules communicate through parameters and use only the necessary data items. Data coupling is considered one of the loosest forms of coupling, as it minimizes dependencies and allows for greater modularity.

## Elements of a Messaging System

### Channel: is a key component that serves as a pathway or medium through which messages are sent and received. Channels are used to direct communication between two or more parties and provide a structured way to organize and deliver messages in a controlled, efficient manner.
### Pipe: acts as a conduit that connects various components (like channels and filters) to facilitate the smooth flow of messages through the system. Pipes transport messages from one component to another, often in sequential steps, allowing for complex message processing.
### Filter: is responsible for examining, transforming, or even discarding messages as they move through the system.

| Element   | Description                                                                                   | Key Functions                                                                                                              | Common Use Cases                                     |
|-----------|-----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------|
| **Channel** | A pathway or medium through which messages are sent and received, directing communication between parties | - Routes messages between senders and receivers<br> - Organizes and segments conversations<br> - Supports asynchronous communication | - One-on-one chats<br> - Group discussions<br> - Broadcast announcements |
| **Pipe**    | A conduit connecting components to ensure message flow across different parts of the system | - Transports messages reliably between components<br> - Decouples system components<br> - Supports pipeline processing | - Connecting channels and filters<br> - Chaining message-processing steps |
| **Filter**  | A component that examines, modifies, or selectively routes messages within the system       | - Validates messages<br> - Transforms message content/format<br> - Enables selective routing based on message content | - Content-based filtering<br> - Message transformation<br> - Security checks |

### Messages

- Has 2 parts: Metadata and Body
- Types:
  - Document: Contains a complete set of information, often used to exchange data between systems. Examples include JSON or XML files with full data records.
  - Command: A directive sent to instruct a system or component to perform a specific action, such as "create user" or "process order." Commands are often part of command-driven architectures.
  - Query: A request for information, where the sender expects a response containing data. Queries are read-only operations, such as fetching user details or retrieving report data.
  - Event: A notification indicating that a specific action or change has occurred within a system, such as "user registered" or "order shipped." Events are commonly used in event-driven architectures to trigger actions in other components.
  - Request/Reply: A bidirectional communication type where the sender issues a request and waits for a response. This type is often used in synchronous communication scenarios where immediate feedback is needed.
 
### Producers

Producers are components that create and send messages to a messaging broker or queue. Producers initiate communication by generating messages, which are then transmitted to **Channels** for routing to their intended destinations.

### Consumers

Consumers are components that receive and process messages. They subscribe to channels or queues to retrieve messages sent by producers, handling each message according to its type and content.

#### Key Concepts for Consumers

**1. Message Acknowledgement**
- Consumers need to acknowledge receipt of each message to ensure reliable processing. Acknowledgment can be managed in two ways:
  - Automatic: The system automatically acknowledges the message once it’s delivered to the consumer, typically used in cases where the processing is lightweight and the system is designed to tolerate occasional message loss.
  - Manual: The consumer explicitly acknowledges a message after successful processing, providing more control but requiring additional logic to handle failures.

**2. Retry Mechanism**
When a consumer fails to process a message, a retry mechanism can attempt to reprocess it. Retries prevent data loss from transient errors and often use exponential backoff to manage retry intervals and reduce load on the system.

**3. Error Handling**
Error handling strategies can include logging errors, sending notifications, or taking corrective action. In some cases, errors are flagged, and the message is held for manual inspection or special processing to prevent message loss.

**4. Dead-Letter Channel**
A Dead-Letter Channel is a designated queue or storage area where unprocessable messages are sent after exceeding retry limits. Dead-letter channels help prevent failed messages from blocking the queue and allow for separate handling, analysis, or inspection of problematic messages.

#### Pull and Push Model

##### Pull Model
- In the **Pull Model**, consumers actively pull messages from the message queue or broker at their own pace. The consumer decides when to request (or poll for) a message, allowing for more controlled and flexible message processing.
- Common in systems like Kafka, where consumers can poll messages at regular intervals.

##### Push Model
- In the **Push Model**, the message broker actively pushes messages to consumers as soon as they are available, allowing for faster message delivery with minimal latency.
- This model is more direct, as consumers receive messages in real-time, based on the broker's delivery rate.

## Topics

Topics are logical channels that enable a publish-subscribe (pub-sub) communication model, allowing messages to be broadcasted to multiple consumers simultaneously. Topics help organize messages by category or subject, making it easier for consumers to receive only relevant information. This model is useful for systems that need to distribute data to multiple recipients in real time.

## Messaging System - Message Broker

- 3 types:
  - Queue-based:
    - Primarily used for tasks that need to be processed asynchronously in a strict order, where each message is processed once and by only one consumer.
    - Messages are stored in a queue and delivered to consumers on a FIFO basis.
    - Ideal for workloads like background job processing, task distribution, or asynchronous communication between services.
    - **Examples:** RabbitMQ, Amazon SQS, ActiveMQ.
  - Cache-based
    - Focused on quick access and retrieval of messages/data, with less emphasis on persistent storage. These brokers are often used for scenarios where real-time speed is crucial and data doesn’t need to be retained permanently.
    - Messages or data are stored in memory, enabling fast read and write access.
    - Commonly used for session storage, temporary data, or low-latency messaging.
    - Not ideal for long-term message storage since messages may be discarded when memory is cleared or when they expire.
    - **Examples:** Redis, Memcached.
  - Stream-based:
    - Used for handling data that flows continuously, often from multiple sources, allowing for both real-time and historical message processing.
    - Supports event streaming, allowing messages to be stored and replayed for multiple consumers.
    - Often used in event-driven architectures, data processing pipelines, real-time analytics, and IoT data ingestion.
    - Messages are usually stored in a log structure, enabling consumers to access the stream at any point.
    - **Examples:** Apache Kafka, Amazon Kinesis, Apache Pulsar.

## Poison Messages

Poison Messages are messages that a system cannot process correctly, usually due to issues like invalid formatting, data corruption, or logical errors. These problematic messages cause the processing system to repeatedly fail when attempting to handle them, often leading to a loop of retries that can clog the message queue and degrade system performance.

### Poison Message Handling Strategies
-**Dead Letter Queues (DLQs):** These are dedicated queues where failed messages are moved after a set number of retries, helping isolate poison messages without blocking other messages.
- **Backoff and Retry Mechanisms:** Implementing retry intervals that increase over time can help mitigate temporary processing issues without overwhelming the system.
- **Message Validation:** Validate message structure and content upon receipt to filter out potential poison messages early.
- **Error Logging and Notification:** Log details about the poison message and notify the appropriate team or system, enabling timely investigation and resolution.

## Load Leveling

Load Leveling is a technique used in distributed systems and cloud computing to balance the workload across resources, ensuring consistent performance even during peak demand. The goal is to prevent any single resource from becoming overwhelmed, thus maintaining steady system responsiveness and reliability.

### Common Load Leveling Techniques
- **Message Queues:** Queue-based load leveling uses message queues to store incoming tasks until resources are available to process them. Systems like RabbitMQ, Amazon SQS, and Azure Service Bus are commonly used for this.
- **Auto-scaling:** Cloud platforms allow for auto-scaling to handle demand surges. When load increases, additional instances of services are created, and when demand decreases, they are removed, optimizing costs and resource usage.
- **Load Balancers:** Distributing incoming traffic across multiple servers, load balancers help ensure no single server becomes a bottleneck. Tools like NGINX, AWS Elastic Load Balancer, and Azure Load Balancer manage this distribution.
- **Throttling and Rate Limiting:** Limiting the rate of requests a system can handle at once helps prevent overload. Requests exceeding the limit are either queued or rejected, maintaining system stability.

## Delivery Guarantees

Delivery guarantees define how reliably a message broker ensures message delivery to its intended recipients. Each level of guarantee provides different trade-offs in terms of complexity, latency, and system reliability.

### At most one (Fire and Forget)

- Low latency: No need to wait for acknowledgments, making it very fast.
- Minimal overhead: Reduced complexity due to lack of retry mechanisms.
- Drawback: If a message is lost in transit, it won’t be resent. Suitable for cases where occasional data loss is acceptable.

### At least one (Acknowledge Delivery) -> Duplicated Processing

- Reliability: Ensures that every message is delivered, even if it means resending.
- Potential for duplicate processing: Due to retries, consumers may receive the same message multiple times and should be able to handle duplicates.
- Drawback: Increased overhead and potential for processing the same message more than once.

### Exactly one (Transaction Delivery) 

- High reliability and consistency: Ensures that each message is processed exactly once, with no duplicates or omissions.
- Higher overhead: Transactional delivery mechanisms can add complexity and may impact performance.
- Drawback: Generally more resource-intensive due to transactional mechanisms.

## Transports

- TCP/IP
- HTTP
- AMQP
- MQTP
- STOMP
- Web Sockets

## Event-Sourcing Architecture

- Event Store: A specialized database that stores all events as immutable records in the order they occurred. It’s the central repository for events, maintaining a complete history of all changes.
- Event: Represents a discrete change in state, such as "OrderPlaced" or "ProductAddedToCart". Events are immutable and contain the necessary information to apply a specific change.
- Aggregate: An entity or group of related entities that are treated as a single unit for the purpose of event handling. Aggregates enforce business rules and validation to ensure data consistency before creating new events.
- Event Handler: Listens for specific events and performs actions in response, often responsible for applying the state changes represented by the event to the aggregat
- Projection: A read-optimized view of the data created by transforming events into a format suited for querying and display. Projections enable efficient querying of the current state without reapplying all events.

**Benefits of Event-Sourcing Architecture**
- Complete audit trail: Every change is captured as an event, creating an immutable log of all state transitions. This allows for a complete audit trail, enabling traceability and accountability for any change.
- Temporal querying: Since every state change is recorded, you can "replay" events to see the system's state at any point in time. This enables historical analysis, debugging, and insights into how the system evolved.
- Scalability: Event-sourcing can naturally support distributed processing by decoupling write operations (event generation) from read operations (projections). Different components can independently handle events, enhancing scalability and responsiveness.
- Resilience: Event-sourcing enhances resilience by making it possible to rebuild system state from events, even if current state representations are corrupted or lost. This is particularly useful for disaster recovery and troubleshooting.

## RabbitMQ

### Exchanges

An exchange is responsible for receiving messages from producers and routing them to the appropriate queues. In RabbitMQ, exchanges use rules defined by their type, routing keys, and headers to determine how to route messages to one or more queues.

#### Exchange Types

```
**Routing Key** is a string that the producer attaches to each message, which helps determine how the message will be routed to queues. Different exchange types handle routing keys differently.
```

```
**Header Attributes:** are set as key-value pairs and provide additional metadata that helps route messages based on multiple attributes.
```

```
A binding key in RabbitMQ is a string used to establish a connection (or binding) between an exchange and a queue. It defines the conditions under which a message sent to the exchange will be routed to the queue. 
```

RabbitMQ supports four main types of exchanges, each with different message routing logic:

- Direct Exchange: Routes messages to queues based on an exact match between the message’s routing key and the queue’s binding key.
  - Messages are only routed to queues whose binding key matches the message’s routing key exactly.
  - Ideal for unicast (single-queue) messaging.
  - Use Case: Targeting specific consumers, such as directing messages to individual services based on a unique identifier.
  - Example: An order service can send messages with routing keys like order.created or order.canceled, routed to queues specifically listening for those events.

- Fanout Exchange: Routes messages to all bound queues, ignoring any routing keys. All queues bound to a fanout exchange will receive a copy of each message sent to that exchange.
  - Broadcasts messages to all queues without filtering.
  - Ideal for multicast (one-to-many) messaging.
  - Use Case: Broadcasting notifications or updates to multiple services or consumers.
  - Example: Broadcasting stock updates to all connected clients in a trading application.

- Topic Exchange: Routes messages to queues based on pattern matching between the routing key and the queue’s binding pattern, allowing for partial matches.
  - Routing keys can include wildcard characters (* to match one word, # to match zero or more words).
  - Flexible and powerful, supporting complex routing requirements.
  - Use Case: Routing messages to queues based on hierarchical topics, such as logs.info, logs.error, or logs.warning.
  - Example: In a logging system, a topic exchange could route logs.* messages to a general log queue, while logs.error routes to a separate error-specific queue.

- Headers Exchange: Routes messages based on message headers rather than routing keys. Messages are routed to queues if the headers match the criteria specified in the queue’s binding.
  - Allows more granular routing based on header attributes.
  - Binding can specify if all headers should match (x-match=all) or if only one match is needed (x-match=any).
  - Use Case: When routing decisions are better suited to specific attributes (e.g., priority, format) instead of a single routing key.
  - Example: A notification system where messages are routed based on headers like priority=high or type=email.













