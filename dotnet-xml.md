# .NET XML
## Get Node Text
I need to do this about once a year and it always takes longer to figure out than it should.  Just do this:
```
internal static string GetText(this XmlNode node)
{
    foreach (XmlNode child in node.ChildNodes)
    {
        if (child.NodeType == XmlNodeType.Text || child.NodeType == XmlNodeType.CDATA)
        {
            return child.Value;
        }
    }

    return null;
}
```
