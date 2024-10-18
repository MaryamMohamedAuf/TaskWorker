Task Management System

## 1. Introduction

The Task Management System is designed to efficiently manage and assign tasks to workers using a message queue architecture. This system leverages RabbitMQ for asynchronous task processing, allowing scalability and improved performance.

## 2. Architecture Overview

The system architecture follows a microservices-like pattern, where the ASP.NET Core Web API serves as the backend. The message queue (RabbitMQ) facilitates communication between the task producer and consumer, ensuring decoupled components.

### Components
- **ASP.NET Core Web API**: Handles client requests and task management operations.
- **RabbitMQ**: Message broker that queues tasks for processing.

## 3. Technology Stack

- **Backend Framework**: ASP.NET Core 6
- **Message Broker**: RabbitMQ
- **Serialization**: Newtonsoft.Json for JSON processing
- **Database**: SQL Server
- **Development Tools**: Visual Studio, Docker (for RabbitMQ)

## 4. Features

- **Task Creation**: Users can create new tasks, which are queued for processing.
- **Task Assignment**: Tasks can be assigned to workers based on availability.
- **Asynchronous Processing**: Tasks are processed in the background, allowing for real-time updates without blocking the main application flow.

## 5. Setup Instructions

1. **Clone the Repository**: 
   ```bash
   git clone <repository-url>
   ```

2. **Run RabbitMQ**:
   - If using Docker:
     ```bash
     docker run -d --hostname rabbitmq --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:management
     ```
   - Access RabbitMQ Management UI at `http://localhost:15672/` (default user: guest, password: guest).

3. **Run the ASP.NET Core Web API**:
   - Open the project in Visual Studio.
   - Restore NuGet packages and run the application.

## 6. RabbitMQ Integration
### advantages

1. **Scalability**: RabbitMQ allows you to handle an increasing number of tasks by decoupling the producer (who assigns tasks) and consumers (workers solving tasks), enabling you to distribute workloads efficiently.
2. **Asynchronous Processing**: Tasks are queued and processed asynchronously, improving system performance, especially when tasks involve time-consuming operations.
3. **Reliability**: RabbitMQ ensures tasks aren’t lost if the system crashes, thanks to its message durability and acknowledgment mechanisms.
4. **Load Balancing**: Distributes tasks evenly among available workers, avoiding overloading any single worker.
5. **Task Prioritization**: Allows you to implement task prioritization, ensuring high-priority tasks are handled before lower-priority ones.

This architecture can be useful in industries like logistics, customer support, financial transactions, and any system where efficient task management and scaling are critical. For example, incoming support tickets can be assigned to the next available agent via RabbitMQ in a large support center. 


### Producer Implementation
The `RabbitMQProducer` class is responsible for sending task messages to the RabbitMQ queue. 

- **Queue Declaration**: The queue is declared with properties such as durability and exclusivity.
- **Message Serialization**: Messages are serialized into JSON before being sent.
- **Message Publishing**: The serialized message is published to the "task" queue.

### Code Example
```csharp
public class RabbitMQProducer : IRabbitMQProducer
{
    public void SendTaskMessage<T>(T message)
    {
        var factory = new ConnectionFactory { HostName = "localhost" };
        using var connection = factory.CreateConnection();
        using var channel = connection.CreateModel();
        
        channel.QueueDeclare("task", false, false, false, null);
        
        var json = JsonConvert.SerializeObject(message);
        var body = Encoding.UTF8.GetBytes(json);
        
        channel.BasicPublish("", "task", null, body);
    }
}
```
##8. References:

https://learn.microsoft.com/en-us/aspnet/core/?view=aspnetcore-8.0
https://www.rabbitmq.com/tutorials/tutorial-two-dotnet
https://www.ibm.com/docs/en/watsonx/watson-orchestrate/current

