# .NET Embedded Resources
## Get Text of Embedded File
I don't have to deal with embedded resources in my assemblies often, but when I do, it's always a painful google search + debugging session. As part of my must-have extension methods list, here's an extension method or two that does it the way I like

```
public static class EmbeddedResourcesExtensionMethods  
{
    public static string GetEmbeddedFileText(this Assembly assembly, string resourceName)
    {
        return GetEmbeddedFileText(assembly, resourceName, () =>
        {
            throw new NotFoundException("Resource {0} was not found in assembly {1}".FormatWith(resourceName, assembly.FullName));
        });
    }

    public static string GetEmbeddedFileText(this Assembly assembly, string resourceName, Func<string> notFoundHadler)
    {
        using (var stream = assembly.GetManifestResourceStream(resourceName))
        {
            if (stream == null)
            {
                if (notFoundHadler != null)
                    return notFoundHadler();
            }
            else
            {
                using (var reader = new StreamReader(stream))
                {
                    return reader.ReadToEnd();
                }
            }
        }

        return null;
    }
}
```
