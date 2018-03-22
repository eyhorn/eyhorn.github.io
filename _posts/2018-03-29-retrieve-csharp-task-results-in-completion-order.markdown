---
layout: post
title:  "Retrieve c# task results in completion order"
date:   2018-03-29 11:24:00 +1100
categories: c# tasks
---

Watching Pluralsight course [Asynchronous C# 5.0](https://www.pluralsight.com/courses/skeet-async) by Jon Skeet I've discovered wonderful extension method that allows to iterate through the tasks in their completion order. I liked it so much that I've decided to write blog post about it.
Here is [github repo](https://github.com/eyhorn/TasksInOrderOfCompletion) with sample code that I will go through in this article.

The starting code will execute same tasks in `Serial` or `Completion` order.

```c#
static void Main(string[] args)
{
    Console.WriteLine("Running tasks one by one");
    Run(ResultCollectionOrder.Serial).Wait();
    Console.WriteLine();
    Console.WriteLine("Running tasks in completion order");
    Run(ResultCollectionOrder.Completion).Wait();
    Console.WriteLine();
    Console.WriteLine("Press any key to close...");
    Console.ReadKey();
}
```

The code that runs the tasks is pretty mundane as well. The only thing that is important to understand that tasks created in `CreateTask` method are returned `hot` (already running) state. At some point I was under impression that task starts running after `await` is applied to it.

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

    var tasksToExecute = resultCollectionOrder == ResultCollectionOrder.Completion ? tasks.InCompletionOrder() : tasks;

    foreach (var task in tasksToExecute)
    {
        var testTaskData = await task;
        Console.WriteLine(testTaskData);
    }
}

static Task<TestTaskData> CreateTask(int id, int delay)
{
    return Task.Delay(delay).ContinueWith(task => new TestTaskData(id));
}
```