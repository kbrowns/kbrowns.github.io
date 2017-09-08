# .NET Pathing

## Get Execution directory
Especially with applications running in the context of IIS/ASP.NET where the physical location on disk is a random cache directory, getting the path of the execution directory at runtime can be a bit tricky. I'm talking about the C:\YourApp\bin directory of your web app. If you're free to reference ASP.NET API's you can get this easily, but if you're writing framework libraries, it requires more work.

I'm willing to bet there's a better way to do this, but the following snippet of code has always done the trick for me

```
public static string GetExecutionDirectory(string @default = ".")  
{
    var executingAssemblyPath = new Uri(Assembly.GetExecutingAssembly().CodeBase).LocalPath;
    return Path.GetDirectoryName(executingAssemblyPath) ?? @default;
}
```
