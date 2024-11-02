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
  - Queue-based
  - Cache-based
  - Stream-based





