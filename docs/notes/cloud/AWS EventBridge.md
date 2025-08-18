
# Amazon EventBridge

Amazon **EventBridge** is a fully managed event bus service that facilitates **event-driven architecture** in AWS. It enables you to **react to events** from AWS services, custom applications, and SaaS providers in real-time. EventBridge allows you to build highly scalable and flexible event-based applications.

Let’s break down the core concepts of Amazon EventBridge in detail:

## 🕰️ EventBridge Components

### 1. **Event Bus**

An **Event Bus** is a central hub that receives, processes, and routes events. EventBridge supports three types of event buses:

- **Default Event Bus**: Automatically created for AWS services like EC2, S3, etc.
- **Partner Event Bus**: For events from AWS SaaS partners (e.g., Zendesk, Datadog).
- **Custom Event Bus**: Created by you for custom events from your applications.

You can configure these event buses to send and receive events across AWS accounts and services.

---

### 2. **Event Patterns**

An **Event Pattern** defines the criteria for matching events. It acts as a filter, ensuring that only events that meet specific conditions are passed to your targets.

#### Example:
If you want to capture events from an EC2 instance state change, you would create an event pattern like this:

```json
{
  "source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": {
    "state": ["stopped"]
  }
}
```

This pattern matches events where an EC2 instance has transitioned to the "stopped" state.

---

### 3. **Scheduled Events (Cron Jobs)**

EventBridge can act like a **cron job** to schedule tasks based on a time interval. You can specify cron expressions to trigger actions at regular intervals (e.g., every hour, every day).

#### Example:
To trigger a Lambda function every hour, you could create a scheduled event pattern like this:

```json
{
  "schedule": "rate(1 hour)"
}
```

This would trigger the event every hour, which could, for instance, trigger a script running in a Lambda function.

---

## 🎯 EventBridge Targets

Once an event is captured, **EventBridge** routes it to **targets**. Targets can be various AWS services or custom endpoints, including:

- **Lambda functions**: To run code in response to events.
- **SNS topics**: To send notifications.
- **SQS queues**: To add messages for later processing.
- **Kinesis Data Streams**: For stream processing.
- **AWS Batch**: To process jobs asynchronously.
- **Step Functions**: To orchestrate workflows.

You can specify one or multiple targets for each event rule.

#### Example:
If an EC2 instance state changes to "stopped," EventBridge can send that event to a Lambda function and also send a notification to an SNS topic.

---

## 🔐 EventBridge Permissions

EventBridge events can be accessed by other AWS accounts using **Resource-based Policies**, which provide fine-grained control over who can publish or consume events.

#### Example:
You can allow Account A to publish events to Account B’s event bus by specifying the permissions in the resource policy of the event bus.

---

## 🗄️ EventArchiving and Replays

### **Event Archiving**

EventBridge allows you to **archive** events that are sent to your event bus. You can archive all events or just a subset based on filters. Archived events can be stored for an indefinite period or for a specific time range (e.g., 30 days). Archiving is beneficial for:

- **Audit purposes**: You can keep track of all events for compliance and debugging.
- **Long-term storage**: Storing historical events to replay or analyze later.

#### Example:
Let’s say you have a system that records customer transactions. You might archive all the event data for the past year for auditing and later replay if needed.

### **Event Replays**

EventBridge allows you to **replay** archived events. This is especially useful when you want to re-trigger actions based on historical events, or troubleshoot and test workflows.

- **Replay archived events**: You can replay events at any time within the retention period.
- **Use cases**: Troubleshooting, re-triggering workflows, or recreating specific conditions for testing.

For instance, if a Lambda function failed to process an event earlier, you can replay the event from the archive to ensure the system behaves correctly.

---

## 🧠 EventBridge Schema Registry

### What is the Schema Registry?

EventBridge can **analyze** the events and **infer the schema** of the data flowing through the event bus. The **Schema Registry** is a feature that allows you to manage and version the structure of your event data. This is useful because:

- It helps you know in advance what kind of data will be inside your events.
- It generates code to parse and handle events without errors.

### How It Works:

1. **Event Analysis**: EventBridge can inspect events and automatically infer the structure (schema) of the data. For example, it can identify fields like `timestamp`, `eventId`, or `eventType`.
2. **Schema Versioning**: You can store multiple versions of the schema as your event data evolves over time.
3. **Code Generation**: Based on the schema, EventBridge can generate **data models** in programming languages like Java, Python, and others. This helps developers avoid errors and ensures compatibility when processing events.

### Example:
Imagine you're sending EC2 instance state change events. EventBridge might infer a schema like this:

```json
{
  "instanceId": "i-1234567890abcdef0",
  "state": "stopped",
  "timestamp": "2021-06-01T12:00:00Z"
}
```

The Schema Registry will allow you to version this schema and provide code to your application that knows exactly how to handle this event.

### Analogy:
Think of the **Schema Registry** as a **data blueprint**. Just like architects use blueprints to know the exact layout of a building, developers can use the schema to know exactly how the event data will be structured. This removes any guesswork when processing the events.

---

## 🧩 Example Use Cases of Amazon EventBridge

### 1. **Real-Time Processing**
You can use EventBridge to **trigger Lambda functions** based on events such as:
- EC2 instance state changes
- S3 object uploads
- DynamoDB table updates

### 2. **Application Integration**
EventBridge allows you to integrate different applications by sending custom events. For example:
- Send data from your application to a custom EventBus.
- Automatically trigger workflows in AWS Step Functions based on events.

### 3. **Automated Scaling**
You can automatically scale your infrastructure by triggering **Auto Scaling actions** in response to events such as high traffic, new users, or system failures.

---

## 📊 EventBridge vs. CloudWatch Events

While EventBridge was originally known as **CloudWatch Events**, EventBridge is now more powerful, as it supports not only AWS services but also **SaaS applications**, **custom events**, and **advanced event patterns**.

| Feature                      | Description |
|------------------------------|-------------|
| **Event Buses**               | Centralized hubs to receive and route events (default, partner, custom) |
| **Event Patterns**            | Filter events based on conditions like source, state, etc. |
| **Scheduled Events (Cron)**   | Schedule tasks using cron expressions (e.g., trigger every 1 hour) |
| **Event Targets**             | AWS services or custom endpoints where events are routed (Lambda, SNS, SQS, etc.) |
| **Archiving & Replays**       | Store and replay events for auditing or troubleshooting |
| **Schema Registry**           | Manage and version event data structures to generate application code |

---

## 🧩 Conclusion

Amazon **EventBridge** is a powerful event-driven service that helps you decouple application components, automate workflows, and integrate multiple services. Whether you're reacting to AWS service events or creating your own custom events, EventBridge provides the flexibility, scalability, and ease of use needed for modern serverless architectures.
