# CoreEvent

This package is an implementation of event aggregator for .NET Core.

In this pattern the subscriber observes the event aggregator instead of the publisher and the publisher knows only about the event aggregator and not about the subscribers.

![alt text](http://magnusmontin.files.wordpress.com/2014/02/eventaggregator.png?w=515&h=155)

# How to use

Run into the package manager console

> Install-Package CoreEvents -Version 1.0.0

Go to startup.cs and configure the service:

```csharp

using CoreEvents;
using CoreEvents.Interfaces;

public void ConfigureServices(IServiceCollection services)
{
  ...
  services.AddSingleton<ICoreEvents, CoreEvent>();
  
}
```

Create a class that will represent the message that the event passed as argument. This class must implement ICoreMessage

```csharp
using CoreEvents.Interfaces;

namespace Common.Events.Messages
{
    public class MessageTest : ICoreMessage
    {
        public string LogMessage { get; set; } = "Hello World";
    }
}
```

This is a sample class that subscribe tha sample message:

```csharp
    public class MessageService
    {
        private readonly ICoreEvents _coreEvent;

        public MessageService(ICoreEvents coreEvent)
        {
            _coreEvent = coreEvent;
            
            // Subscribing the event
            _coreEvent.Subscribe<MessageTest>(c => { MessageNotification(c); });
        }

        // This method will be called when someone publish MessageTest
        private void MessageNotification(MessageTest message)
        {
            Console.WriteLine($"Received a notification from: {message.LogMessage}");
        }
    }
```

The next sample is a method publishing MessageTest.

```csharp
  public class PublishSample
  {

      private readonly ICoreEvents _coreEvent;

      public PublishSample(ICoreEvents coreEvent)
      {
          _coreEvent = coreEvent;
          
          // The message will be published for those who have subscribed to it
          _coreEvent.Publish(new MessageTest());
      }
  }
```
