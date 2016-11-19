---
layout:     post
title:      "Set NuGet pre-release build numbers with leading zeros on TeamCity"
date:       2014-11-23 22:34:12 +0100
comments:   true
categories: [Develop,MahApps.Metro,Tips&Tricks]
tags:       [NuGet,TeamCity,CI,Powershell,MahApps.Metro]
---

Sometimes it comes the day that [NuGet](https://www.nuget.org/) doesn't see a newer version of a pre-release package. So if you wanted to update to a newer version you must using the `Package Manager Console` with the `-pre` flag. That's because `NuGet` makes a alpanumeric comparison, so e.g. **ALPHA99** is greater than **ALPHA115**.

So for pre-releases we can use the **Rule 3** from the [Top 10 NuGet (Anti-) Patterns](http://msdn.microsoft.com/en-us/magazine/jj851071.aspx): Use leading zeros in the numerical suffix to auto-increment pre-releases.

And here comes my `getting gray hairs` part. The `powershell` build step doesn't work with the allowed string number formatting like this:

```powershell
"{0:D3}" -f 2
```

Expected result:

```powershell
002
```

But on `TeamCity` I got:

```powershell
2
```

So after trial and error I used this `powershell` script with the `PadLeft` function:

```powershell
$buildNumber = $env:BUILD_NUMBER
$VersionComponents = $buildNumber.Split(".")
$counter = ("{0}" -f $VersionComponents[3]).PadLeft(3, "0")
$buildNumber = "{0}.{1}.{2}-ALPHA{3}" -f $VersionComponents[0] , $VersionComponents[1] , $VersionComponents[2] , $counter
Write-Host "##teamcity[setParameter name='env.BUILD_NUMBER' value='$buildNumber']"
```

This gives me e.g:

```powershell
1.0.0-ALPHA001
```

Use this script to change the `TeamCity` build number before creating the `NuGet` package.

Hope this helps!
