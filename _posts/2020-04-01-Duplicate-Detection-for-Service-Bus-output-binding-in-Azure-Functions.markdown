---
layout: post
title: Duplicate Detection for Service Bus output binding in Azure Functions
date: 2020-04-01T00:00:00.0000000-07:00
categories: []
tags: []
published: true
permalink: 
featuredImageUrl: 
---
Here's a way to get the Azure Service Bus output binding for your Azure Function working after you've enabled Duplicate Detection (or other features) on the Service Bus queue or topic.

If you haven't had duplicate detection turned on or haven't been using some of the other Azure Service Bus features, you might set up the output binding in your Azure Functions so you can send a custom class object to a Service Bus queue or topic. Something like the following.

```csharp
[FunctionName("MyFunction1")]
public static async Task<IActionResult> Run(
[HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
[ServiceBus("%MyQueueName%",
Connection = "MyServiceBusConnection")] IAsyncCollector<MyItem> myOutputQueue)
{
    var myItem = new MyItem();

    // Do stuff.
    // . . .

    // Add the item to the queue.
    await myOutputQueue.AddAsync(myItem);

    // Do more stuff.
    // . . .

    return new OkResult();
}
```
Notice that the Service Bus out parameter specifies the custom class we want to send to the to the queue or topic. In the example the custom class is `MyItem`.
```csharp
IAsyncCollector<MyItem>
```

That will work great as long as you don't have Duplicate Detection (or some of the other features) enabled for the queue or topic. 

See Azure Service Bus Docs: [Duplicate detection](https://docs.microsoft.com/en-us/azure/service-bus-messaging/duplicate-detection) and [Messages, payloads, and serialization](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messages-payloads) for more information about Service Bus Duplicate Detection.

### Throws When Service Bus has Duplicate Detection Enabled
If you enable Duplicate Detection on the queue or topic, your function will throw an
`System.InvalidOperationException` that has a message that starts with:

```
Message to a partitioned entity with duplicate detection enabled must have either PartitionKey or MessageId set . . .
```

### A Solution to Support Service Bus Duplicate Detection
One way to make it work when duplicate detection is enabled is to output a `Micosoft.Azure.ServiceBus.Message` that contains your custom class, and then set the `MessageId` on the `Message` object to be added to the queue or topic.

This approach will work whether or not duplicate detection is enabled on the queue or topic. You can also use the approach to take advantage of other Azure Service Bus features that require the settings of properties on the `Micosoft.Azure.ServiceBus.Message` objects.

```csharp
[FunctionName("MyFunction2")]
public static async Task<IActionResult> Run(
[HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
[ServiceBus("%MyQueueName%",
Connection = "MyServiceBusConnection")] IAsyncCollector<Message> myOutputQueue)
{
    var myItem = new MyItem();

    // Do stuff.
    // . . . 

    // Add the item to the queue, wrapped in a Message.
    string json = JsonConvert.SerializeObject(myItem);
    Message outputMessage = new Message(Encoding.UTF8.GetBytes(json))
    {
        ContentType = "application/json",

        // Set the MessageId for use in duplicate detection.

        MessageId = $"{myItem.TypeId}-{myItem.ItemId}" 
    };

    await myOutputQueue.AddAsync(outputMessage);

    // Do more stuff.
    // . . .

    return new OkResult();
}
```

Notice that we changed the Service Bus out parameter class type to `Micosoft.Azure.ServiceBus.Message`.
```csharp
    IAsyncCollector<Message>
```
And we wrapped our custom class object (`MyItem` in the example) in a `Message`, and set its `MessageId` property.

```csharp
    string json = JsonConvert.SerializeObject(myItem);
    Message outputMessage = new Message(Encoding.UTF8.GetBytes(json))
    {
        ContentType = "application/json",

        // Set the MessageId for use in duplicate detection.

        MessageId = $"{myItem.TypeId}-{myItem.ItemId}" 
    };

    await myOutputQueue.AddAsync(outputMessage); 
```

### Under the Hood of the Microsoft.Azure.WebJobs.Extensions.ServiceBus
The source code in GitHub for Microsoft.Azure.WebJobs.Extensions.ServiceBus shows how it handles the difference between a custom class and Microsoft.Azure.ServiceBus.Message.

See `MessageConverterFactory.cs` at: [https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/Bindings/MessageConverterFactory.cs](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/Bindings/MessageConverterFactory.cs)

And `UserTypeToBrokeredMessageConverter.cs` at: [https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/Bindings/UserTypeToBrokeredMessageConverter.cs](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/Bindings/UserTypeToBrokeredMessageConverter.cs)

When a custom class is detected by the `MessageConverterFactory` it calls the `UserTypeToBrokeredMessageConverter` to create a `Micosoft.Azure.ServiceBus.Message` to wrap it. So we can see that under the hood, everything put in a Azure Service Bus queue or topic is a `Message`.