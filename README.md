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



