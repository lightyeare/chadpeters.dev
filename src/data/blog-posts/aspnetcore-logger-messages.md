---
title: ASP.NET Core Logger messages matter
slug: aspnetcore-logger-messages
publishDate: 25 July 2024
description: A look at how different types of messages are logged in ASP.NET Core Logger.
---

_This post was originally published on the [RIMdev Blog](https://rimdev.io/aspnetcore-logger-messages)_

Recently I was reviewing some code and I noticed something that seemed a little strange 😕

```csharp
catch (Exception ex)
{
    _logger.LogError("Unable to retrieve Lead {0} : {1}", leadId, ex);
    return null;
}
```

Admittedly, I didn't know as much about logging as I should have 🤷. It's just something that was always there that someone else setup. It did seem strange that you would stuff the exception into the message. So as they say, there is no time like the present! ⛏

ASP.NET Core makes it easy to connect to a variety of logging providers. You can find out more about setting up logging [here](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/logging/?view=aspnetcore-8.0). I want to focus on the messages you pass to the logger. 

The messages get passed using a message template. This allows for [symantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging). I read that was important because logging providers can store the parameters in the message template as fields. What does that look like and why is that important? Sometimes I need to see it in action to cement my understanding.

I ran `dotnet new webapp` to setup a new ASP.NET Core project. In the project's Program.cs file I added this contrived endpoint:

```csharp
app.MapGet("/log/{id}", async (int id, ILogger<Program> logger, HttpResponse response) =>
{
    logger.LogInformation("Testing logging in Program.cs");
    try
    {
        throw new ArgumentException("Invalid ID","id");
    }
    catch (Exception ex)
    {
        logger.LogError("Message template with exception | There has been an error for id {id}: {exception}", id, ex);
        logger.LogError($"String interpolation | There has been an error for id {id}: {ex}");
        logger.LogError(ex, "Message template, exception as parameter | There has been an error for id {id}", id);
    }

    await response.WriteAsync("Success?");
});

```

🔎 Let's compare the first two `logger.LogError`. The first one uses the message template as recommended. This looks like what you would see in the `string.Format` method. This might lead you to think, "Why don't we use string interpolation instead?" like we see in the second log. 

I configured my logger in my sample app to write to Azure Application Insights (see [Using Application Insights in ASP.NET Core](https://learn.microsoft.com/en-us/azure/azure-monitor/app/asp-net-core)). After running my app and hitting the endpoint, I found my two errors in the LogManagement.AppTraces table of my Log Analytics workspace Logs. I've eliminated some properties for brevity. 

![Message Template](/assets/blog/logger/messagetemplate.jpg)

![String Interpolation](/assets/blog/logger/stringinterpolation.jpg)

Comparing the two, you can spot a few differences. The Properties.OriginalFormat in the message template example shows the templated message. In the string interpolation example it shows the dynamic, interpolated string. The message template example also includes the two parameters, `id` and `exception`, as Properties.

💡 Why is this important? It's important because now querying my logs becomes much easier. If we use the message template, I can now query Properties.OriginalFormat for the message template and get all instances of this error. If we use string interpolation the OriginalFormat is dynamic because it will have different ids. We can also narrow our queries further by using the `id` and `exception` properties in our queries.

Let's look at the third example. `Microsoft.Extensions.Logging` has many overloads for ILogger.LogError. One of those overloads takes the exception as the first parameter and the message template as the second. What do we get for this? 

My log message now appears in the LogManagement.AppExceptions table and I get more properties related to the exception that I can now query.

![Exception and Message Template](/assets/blog/logger/exception.jpg)

> Since the message template does look like the `string.Format` method it might also be tempting to use numbers for your parameter placeholders i.e. `The error is {0} at {1}`. It won't benefit you to have a bunch of different log message types with a property whose name is `0`. Also keep in mind that templating works on the **order of the parameters**, not the names. 

It helped me to see this for myself in Log Analytics. If you've never seen this before either, hopefully it helped you too.

If you want to see the source code for the sample app visit the [GitHub repo](https://github.com/lightyeare/AppInsightsLogging).

_This post was originally published on the [RIMdev Blog](https://rimdev.io/aspnetcore-logger-messages)_