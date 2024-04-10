+++
title = 'Code Obfuscation for .NET MAUI (Target to Android)'
summary = 'How to security your MAUI app to defends from reverse engineering'
description = 'How to security your MAUI app to defends from reverse engineering'
date = 2024-04-11T00:20:00+08:00
series = [".NET", "Security"]
weight = 1
aliases = ["/protect-dotnet-code-4"]
tags = [".NET", ".NET MAUI", "Security", "Reverse Engineering", "Obfuscation", "R8"]
author = ["Tim"]
draft = false
ShowToc = true
ShowReadingTime = true
+++

## How to
Luckily, obfuscating the Android app during the building process is already provided by Google, so just copying the following code into the `<Project>` element in the `.csproj` file is enough. For more details, please see the reference. [^1] [^2]

```
<PropertyGroup Condition="'$(Configuration)|$(TargetFramework)|$(Platform)'=='Release|net8.0-android|AnyCPU'">
    <AndroidLinkTool>r8</AndroidLinkTool>
</PropertyGroup>
```

## Reference
[^1]: Jonathan Peppers et al. (2018). What is D8? What is R8?. Retrieved from https://github.com/xamarin/xamarin-android/blob/main/Documentation/guides/D8andR8.md#what-is-d8-what-is-r8
[^2]: Google. (2024). Shrink, obfuscate, and optimize your app - Android Developers. Retrieved from https://web.archive.org/web/20240408175349/https://developer.android.com/build/shrink-code?authuser=1