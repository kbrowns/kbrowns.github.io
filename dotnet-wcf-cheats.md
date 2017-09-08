# .NET WCF Cheats
These are cheats for the System.ServiceModel space that I've compiled over the years

```
public static class WCFExtensionMethods  
{
    public static IEnumerable<PropertyInfo> GetDataMemberProperties(this Type type)
    {
        return type.GetProperties().Where(x => x.IsDataMember());
    }

    public static IEnumerable<FieldInfo> GetDataMemberFields(this Type type)
    {
        return type.GetFields().Where(x => x.IsDataMember());
    }

    public static IEnumerable<PropertyInfo> GetMessageMemberProperties(this Type type)
    {
        return type.GetProperties().Where(x => x.IsMessageMember());
    }

    public static IEnumerable<FieldInfo> GetMessageMemberFields(this Type type)
    {
        return type.GetFields().Where(x => x.IsMessageMember());
    }

    public static bool IsDataContract(this Type type)
    {
        if (type == null)
            return false;

        return type.HasAttribute<DataContractAttribute>();
    }

    public static bool IsDataMember(this PropertyInfo property)
    {
        if (property == null)
            return false;

        return property.HasAttribute<DataMemberAttribute>();
    }

    public static bool IsDataMember(this FieldInfo field)
    {
        if (field == null)
            return false;

        return field.HasAttribute<DataMemberAttribute>();
    }

    public static bool IsMessageContract(this Type type)
    {
        if (type == null)
            return false;

        return type.HasAttribute<MessageContractAttribute>();
    }

    public static bool IsMessageMember(this PropertyInfo property)
    {
        if (property == null)
            return false;

        return property.HasAttribute<MessageBodyMemberAttribute>() || property.HasAttribute<MessageBodyMemberExtendedAttribute>();
    }

    public static bool IsMessageMember(this FieldInfo field)
    {
        if (field == null)
            return false;

        return field.HasAttribute<MessageBodyMemberAttribute>() ||
                field.HasAttribute<MessageBodyMemberExtendedAttribute>() ||
                field.HasAttribute<MessageHeaderAttribute>();
    }
}
```
