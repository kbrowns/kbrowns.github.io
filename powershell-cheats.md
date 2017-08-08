# Powershell Cheats
This is a compilation of powershell snippets to do various things.  I can never remember the cryptic powershell syntax and API's, so I use this cheatsheet just about every time I touch the language.

## Define a Function
pretty straightforward, but I found parameterization to be really weird
```
function do_something{
  param ([string]$parameter1, [string]$parameter2)
  Write-Host 'did something'
  Write-Host "parameter1: ${parameter1}"
  Write-Host "parameter2: ${parameter2}"
}
```

## Make Function Parameters Required
```
function do_something{
  param ([string]$parameter1=$(throw 'parameter1 is a required parameter'))
}
```

## Call a Function
Don't use commas!
```
do_something 'hello' 'world'
```

## Execute Script
Pretty straightforward.  From cmd.exe:
```
@powershell .\my-script.ps1
```
From powershell.exe:
```
.\my-script.ps1
```
In any case, the path must be absolute

## Execute Script with Parameters
You need to put one of these funky param blocks at the top of your script:

```
param ( [string]$foo)
```
And then pass the parameter on the command line in a named fashion like so:
```
@powershell .\my-script -foo 'bar'
```

## Treat Errors Like.... Errors
There's no guarantee that errors will actually stop execution as you might expect, but having the following at the top makes powershell behavior much more inline with what you'd expect
```
$ErrorActionPreference = "Stop"
```

## Constrain Powershell Version
Add the following to the top of your script and the version of powershell will be constrained at runtime accordingly
```
#Requires -Version 4.0
```

## Suppress Command Output
Sometimes you want to execute something like a directory delete and you don't want the detailed console output displayed.  In *nix you'd do something like this:  >dev/null.  In powershell, you'd do this: | out-null.  Such as:

```
New-Item -ItemType directory -Path $dir | out-null
```

## Execute External Process
There's a few ways to do this... the only way I found that waits properly and handles errors properly is:

```
&something.exe -arg1 'foo' -arg2 'bar'
if ($lastexitcode -ne 0) { throw 'something.exe failed' }
```

## Read File and Display Content
```
Get-Content 'path\to\file.txt'
```

## Recreate Directory If It Exists
```
$dir = 'path\to\dir'
If (Test-Path $dir) { Remove-Item $dir -Force -Recurse | out-null }

New-Item -ItemType directory -Path $dir | out-null
```

## Get Installed Programs
```
function get_installed_apps
{
    if ([IntPtr]::Size -eq 4) {
        $regpath = 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*'
    }
    else {
        $regpath = @(
            'HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*'
            'HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*'
        )
    }
    Get-ItemProperty $regpath | .{process{if($_.DisplayName -and $_.UninstallString) { $_ } }} | Select DisplayName, Publisher, InstallDate, DisplayVersion, UninstallString |Sort DisplayName
}
```

## Check If App Is Installed
This uses the function above in the Get Installed Programs section
```
function inspect_for_app {
  param ([string] $name,
         [string] $targetVersion)

  Write-Host -NoNewLine " * Inspecting ${name}:  " -foregroundcolor Green
  $appToMatch = "$name*"
  $app = get_installed_apps | where {$_.DisplayName -like $appToMatch}

  if ($app -eq $null -or $app.DisplayVersion -eq $null -or $app.DisplayVersion -eq '') {
    Write-Host "No" -foregroundcolor Green
  } else {
    $version = $app.DisplayVersion
    Write-Host "$version, $targetVersion" -foregroundcolor Green
  }

  return $app
}

$vagrantApp = inspect_for_app 'Vagrant' $targetVagrant
$vagrantVersion = $vagrantApp.DisplayVersion
```

## Check Is Administrator
If you script needs to run as elevated, this will do it:
```
if (-NOT ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole(`
  [Security.Principal.WindowsBuiltInRole] "Administrator")) {

}
```

## Parse JSON
```
$versionsJson = Get-Content $versionTempPath | Out-String | ConvertFrom-Json
```

## HTTP GET
performs an HTTP GET for an XML payload and interprets it
```    
$url = "http://scm.csgicorp.com/buildhistory/api/modules/CSGCloudCMDB/versions/latest/builds/latest?links=fals"
$webClient = New-Object System.Net.WebClient
$webClient.Headers.add('content-type','text/xml')
$xml = $webClient.DownloadString($url)
$doc = [xml]($xml)
Write-Host $doc.Response.Build
```

## Download File
```
function download_file{
  param ([string]$url, [string] $dest)
    $webClient = New-Object System.Net.WebClient
    $webClient.DownloadFile($url, $dest)
}
```
