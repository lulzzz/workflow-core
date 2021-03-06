# Events

A workflow can also wait for an external event before proceeding.  In the following example, the workflow will wait for an event called *"MyEvent"* with a key of *0*.  Once an external source has fired this event, the workflow will wake up and continue processing, passing the data generated by the event onto the next step.

```C#
public class EventSampleWorkflow : IWorkflow<MyDataClass>
{
    public void Build(IWorkflowBuilder<MyDataClass> builder)
    {
        builder
            .StartWith(context => ExecutionResult.Next())
            .WaitFor("MyEvent", data => "0")
                .Output(data => data.Value, step => step.EventData)
            .Then<CustomMessage>()
                .Input(step => step.Message, data => "The data from the event is " + data.Value);
    }
}
...
//External events are published via the host
//All workflows that have subscribed to MyEvent 0, will be passed "hello"
host.PublishEvent("MyEvent", "0", "hello");
```

## Effective Date

You can also specify an effective date when waiting for events, which allows you to respond to events that may have already occurred in the past, or only ones that occur after the effective date.
