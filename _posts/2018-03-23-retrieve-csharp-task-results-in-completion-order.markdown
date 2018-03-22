---
layout: post
title:  "Retrieve c# task results in completion order"
date:   2018-03-23 11:24:00 +1100
categories: c# tasks
---

Watching Pluralsight course [Asynchronous C# 5.0](https://www.pluralsight.com/courses/skeet-async) by Jon Skeet I've discovered a wonderful extension method that allows to iterate through the tasks in their completion order. I liked it so much that I've decided to write a blog post about it.
Here is the [github repo](https://github.com/eyhorn/TasksInOrderOfCompletion) with sample code that I will go through in this article.

The starting code will process task results in `Serial` and `Completion` orders.

```c#
static void Main(string[] args)
{
    Console.WriteLine("Retrieving task results one by one");
    Run(ResultCollectionOrder.Serial).Wait();
    Console.WriteLine();
    Console.WriteLine("Retrieving task results in completion order");
    Run(ResultCollectionOrder.Completion).Wait();
    Console.WriteLine();
    Console.WriteLine("Press any key to close...");
    Console.ReadKey();
}
```

The code that runs the tasks is pretty mundane as well. The only thing that is important to understand is that tasks created in the `CreateTask` method are returned `hot` (already running) state. At some point I was under impression that task starts running only after `await` is applied to it.

```c#
static async Task Run(ResultCollectionOrder resultCollectionOrder)
{
    var tasks = new List<Task<TestTaskData>>
    {
        CreateTask(1, 800),
        CreateTask(2, 200),
        CreateTask(3, 150),
        CreateTask(4, 400)
    };

    var tasksToExecute = resultCollectionOrder == ResultCollectionOrder.Completion
        ? tasks.InCompletionOrder()
        : tasks;

    foreach (var task in tasksToExecute)
    {
        var testTaskData = await task;
        Console.WriteLine(testTaskData);
    }
}

static Task<TestTaskData> CreateTask(int id, int delay)
{
    return Task.Delay(delay).ContinueWith(task => new TestTaskData(id, delay));
}
```

`TestTaskData` is a very simple class that is used only to illustrate the order in which task results are processed.

```c#
internal class TestTaskData
{
    public TestTaskData(int id, int delay)
    {
        _id = id;
        _delay = delay;
    }

    private readonly int _id;
    private readonly int _delay;

    public override string ToString()
    {
        return $"{_id} with delay {_delay}";
    }
}
```

The results of the execution would be as follows:

```text
Retrieving task results one by one
1 with delay 800
2 with delay 200
3 with delay 150
4 with delay 400

Retrieving task results in completion order
3 with delay 150
2 with delay 200
4 with delay 400
1 with delay 800
```

If you writing an app that needs to process task results as soon as they come back you will be getting results sooner. In this case you wouldn't have to wait 800 milliseconds to get your first result. And if this information is displayed on the UI it could mean much better user experience.

Now let's take a look at the extension itself that allows us to process task results in completion order.

```c#
public static class TaskExtensions
{
    public static IEnumerable<Task<T>> InCompletionOrder<T>(this IEnumerable<Task<T>> tasks)
    {
        var tasksArray = tasks as Task<T>[] ?? tasks.ToArray();
        var completionSources = tasksArray.Select(task => new TaskCompletionSource<T>())
            .ToArray();
        int taskIndex = -1;

        foreach (var task in tasksArray)
        {
            task.ContinueWith(completedTask =>
                    completionSources[Interlocked.Increment(ref taskIndex)]
                        .PopulateFromCompletedTask(completedTask),
                TaskContinuationOptions.ExecuteSynchronously);
        }

        return completionSources.Select(source => source.Task);
    }

    public static void PopulateFromCompletedTask<T>(this TaskCompletionSource<T> completionSource,
        Task<T> task)
    {
        switch (task.Status)
        {
            case TaskStatus.RanToCompletion:
                completionSource.TrySetResult(task.Result);
                break;
            case TaskStatus.Faulted:
                completionSource.TrySetException(task.Exception.InnerExceptions);
                break;
            case TaskStatus.Canceled:
                completionSource.TrySetCanceled();
                break;
            default:
                throw new ArgumentException("Task is not in a completed state");
        }
    }
}
```

Let's now look at important pieces of this extension.
First we create array of `TaskCompletionSource` of the same size as the number of tasks we are processing.

```c#
var completionSources = tasksArray.Select(task => new TaskCompletionSource<T>()).ToArray();
```

Next we iterate over all input tasks and attaching to the continuation. Inside the continuation delegate we are taking the next completion source and populating it with results of the completed task. `Interlocked.Increment(ref taskIndex)` insures that we are accessing completion sources in thread safe manner. As you've seen from the setup code above tasks are already running and as they complete the continuation will be called for each one of them. We are using this to populate `TaskCompletionSource` array with the results in completion order.

```c#
foreach (var task in tasksArray)
{
    task.ContinueWith(completedTask =>
            completionSources[Interlocked.Increment(ref taskIndex)]
                .PopulateFromCompletedTask(completedTask),
        TaskContinuationOptions.ExecuteSynchronously);
}
```

And finally we returning tasks for the `TaskCompletionSource` array to the caller to iterate over.

```c#
return completionSources.Select(source => source.Task);
```

Information about this extension is available from multiple sources but I think it deserves many more mentions.