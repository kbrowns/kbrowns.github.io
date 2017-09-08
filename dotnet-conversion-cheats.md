# .NET Type Conversion Cheats

## Generic Type Parsing
I'm great and making this harder than it needs to be.  Just do this:
```
/// <exception cref="ArgumentException"></exception>
public static T Parse<T>(this string value)
{
    try
    {
        var converter = TypeDescriptor.GetConverter(typeof(T));
        return (T)converter.ConvertFromString(value);
    }
    catch
    {
        throw new ArgumentException(string.Format("Unable to parse the value: {0} to type: {1}", value, typeof(T).FullName));
    }
}
```
