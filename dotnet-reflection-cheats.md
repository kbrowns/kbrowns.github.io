# .NET Reflection Cheats

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;

public static class ReflectionExtensionMethods
{
    public static bool HasAttribute<T>(this MemberInfo member) where T : Attribute
    {
        return member.HasAttribute<T>(true);
    }

    public static bool HasAttribute<T>(this MemberInfo member, bool inherit) where T : Attribute
    {
        return member.GetAttribute<T>(inherit) != null;
    }
    public static T GetAttribute<T>(this MemberInfo member) where T : Attribute
    {
        return member.GetAttribute<T>(true);
    }

    public static T GetAttribute<T>(this MemberInfo member, bool inherit) where T : Attribute
    {
        if (member == null)
            return null;

        var attributes = member.GetCustomAttributes(typeof (T), inherit);

        if (attributes.Length > 0)
            return (T)attributes[0];

        return null;
    }

    public static T GetAttribute<T>(this Type type) where T : Attribute
    {
        return GetAttribute<T>(type, true);
    }

    public static T GetAttribute<T>(this Type type, bool inherit) where T : Attribute
    {
        if (type == null)
            return null;

        var attributes = type.GetCustomAttributes(typeof (T), inherit);

        if (attributes.Length > 0)
            return (T)attributes[0];

        return null;


    }

    public static bool IsString(this Type type)
    {
        return type == typeof(string);
    }

    public static bool IsConcreteNonStringClass(this Type type)
    {
        if (type == null)
            return false;

        return type.IsClass && !type.IsAbstract && !type.IsString() && !type.IsArray;
    }

    public static bool IsNonStringClass(this Type type)
    {
        if (type == null)
            return false;

        return type.IsClass && !type.IsString();
    }

    public static T CreateInstanceAs<T>(this Type type) where T : class
    {
        try
        {
            var instance = Activator.CreateInstance(type);

            T casted = instance as T;

            if (casted == null)
                throw new ConventionViolationException("Was able to construct type {0}, but it does not implement the expected interface or base class {1}.".FormatWith(type, typeof(T)));

            return casted;
        }
        catch (Exception ex)
        {
            throw new ConventionViolationException("Failed to construct a valid object from type {0}.  See the inner exception for details.".FormatWith(type), ex);
        }
    }

    public static bool IsEnumerable(this Type type)
    {
        return (typeof(IEnumerable).IsAssignableFrom(type));
    }

    public static bool IsCollection(this Type type)
    {
        return (typeof(ICollection).IsAssignableFrom(type));
    }

    public static string GetFriendlyName(this Type type, bool displayFullInnerNames = false)
    {
        if (type == null)
            return null;

        if (type.IsGenericType)
        {
            return BuildGenericFriendlyName(type.Name, type.GetGenericArguments(), displayFullInnerNames);
        }

        return type.Name;
    }

    public static string GetFriendlyFullName(this Type type, bool displayFullInnerNames = false)
    {
        if (type == null)
            return null;

        if (type.IsGenericType)
        {
            return BuildGenericFriendlyName(type.FullName, type.GetGenericArguments(), displayFullInnerNames);
        }

        return type.FullName + ", " + type.Assembly.GetName().Name;
    }

    private static string BuildGenericFriendlyName(string name, Type[] args, bool displayFullInnerNames)
    {
        StringBuilder buffer = new StringBuilder();
        buffer.Append(name.Substring(0, name.IndexOf("`", StringComparison.Ordinal)));
        buffer.Append("<");


        for (int i = 0; i < args.Length; i++)
        {
            if (i > 0) buffer.Append(", ");

            if (displayFullInnerNames)
                buffer.Append(args[i].FullName);
            else
                buffer.Append(args[i].Name);
        }

        buffer.Append(">");

        return buffer.ToString();
    }

    public static Type GetEnumeratorType(this Type type)
    {
        if (!typeof(IEnumerable).IsAssignableFrom(type))
            throw new ArgumentException("Invalid GetEnumeratorType() usage - {0} must implement interface {1}.".FormatWith(type.FullName, typeof(IEnumerable).FullName));

        if (type.IsArray)
            return type.GetElementType();

        Type currentType = type;

        while (currentType != null && currentType != typeof(object) && typeof(IEnumerable).IsAssignableFrom(currentType))
        {
            if (currentType.IsGenericType)
            {
                Type[] genericArguments = currentType.GetGenericArguments();

                if (genericArguments.Length == 1)
                    return genericArguments[0];
            }

            currentType = currentType.BaseType;
        }

        return typeof(object);
    }

    public static Type GetMemberType(this MemberInfo member)
    {
        PropertyInfo property = member as PropertyInfo;

        if (property != null)
            return property.PropertyType;

        FieldInfo field = member as FieldInfo;

        if (field != null)
            return field.FieldType;

        throw new ConventionViolationException(String.Format("Member mapping is only supported for fields or properties. '{0}' declared on '{1}' is an unrecognized MemberInfo type.", member.GetType(), member.DeclaringType));
    }

    public static bool IsConcreteClass(this Type type)
    {
        if (type == null)
            return false;

        return type.IsClass && !type.IsAbstract;
    }

    public static string SimpleAssemblyQualifiedName(this Type type)
    {
        if (type == null)
            return null;

        return String.Format("{0}, {1}", type.FullName, type.Assembly.GetName().Name);
    }

    public static bool IsNullableDataType(this PropertyInfo property)
    {
        return property.PropertyType.IsNullableDataType();
    }

    public static bool IsNullableDataType(this FieldInfo field)
    {
        return field.FieldType.IsNullableDataType();
    }

    public static bool IsNullableDataType(this Type type)
    {
        if (type.IsEnum)
            return type.HasZeroValue();

        if (type.IsClass)
            return true;

        if (type.IsGenericType && type.GetGenericTypeDefinition() == typeof(Nullable<>))
            return true;

        return false;
    }

    public static bool HasZeroValue(this Type type)
    {
        var values = Enum.GetValues(type);

        if (values.Cast<object>().Any(value => 0 == (int)value))
        {
            return true;
        }

        return false;
    }

    public static bool IsOfType<T>(this PropertyInfo property)
    {
        if (property.PropertyType == typeof(T))
            return true;

        return false;
    }

    public static bool IsOfType<T>(this PropertyInfo property, bool includeNullables) where T : struct
    {
        if (property.PropertyType == typeof(T))
            return true;

        if (includeNullables && property.PropertyType == typeof(T?))
            return true;

        return false;
    }

    public static bool IsOfType<T>(this FieldInfo field)
    {
        if (field.FieldType == typeof(T))
            return true;

        return false;
    }

    public static bool IsOfType<T>(this FieldInfo field, bool includeNullables) where T : struct
    {
        if (field.FieldType == typeof(T))
            return true;

        if (includeNullables && field.FieldType == typeof(T?))
            return true;

        return false;
    }

}
```
