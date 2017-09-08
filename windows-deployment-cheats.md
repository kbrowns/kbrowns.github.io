# Windows Deployment Cheats
Something tells me this will be a pretty lonely page, but there's one cheat I need
regularly

## MSI Logging
When an MSI fails it gives you next to no information.  You have to run it from the
commandline with logging enabled like so:

```
msiexec /i YouInstaller.msi /l*v .\log.txt  
```
