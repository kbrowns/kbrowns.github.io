# .NET Security Cheats

## Detect Elevation
```
var principal = new WindowsPrincipal(WindowsIdentity.GetCurrent());
var isElevated = (principal.IsInRole(WindowsBuiltInRole.Administrator) || principal.IsInRole(0x200)).ToString();
Console.WriteLine($"Is Elevated?  {isElevated}");
```