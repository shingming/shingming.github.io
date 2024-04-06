+++
title = 'Protect .NET Code from Reverse Engineering'
summary = 'The strategy of .NET apps defends from reverse engineering'
description = 'The strategy of .NET apps defends from reverse engineering'
date = 2024-04-07T02:10:00+08:00
series = [".NET", "Security"]
weight = 1
aliases = ["/protect-dotnet-code-1"]
tags = [".NET", "Security", "Reverse Engineering"]
author = ["Tim"]
draft = false
ShowToc = false
ShowReadingTime = true
+++

###### tags: Security, .NET, Reverse Engineering

## Background
As software developers, we are responsible for safeguarding the application's security in all aspects, from design to deployment. One aspect of the application's security that you should care about is the building process, especially when building .NET applications (Will explain why in the following). Adopting safe building strategies such as code obfuscation can help to secure trade secrets and other intellectual property (IP), reduce piracy and counterfeiting, and protect against tampering and unauthorized debugging.

## .NET Assembly
A compiled .NET assembly contains Intermediate Language (CIL) instructions, resources, and metadata describing the types, methods, properties, fields, and events in the assembly. [^1] [^2]

Reverse engineers or hackers can use these rich "materials" to reverse or decompile it without any effort, generating high-level code that makes code more readable and finally achieving their purpose - stealing and undermining the application author's trade secrets and IP.

## Code Obfuscation
One technique to defend reverse engineering is code obfuscation. It creates or modifies code that is difficult for humans or computers to understand, but program output isn't affected. Different techniques are used in the obfuscation transforms, including renaming, string encryption, and control flow obfuscation. [^3] [^4]

Referring to the comprehensive survey from obfuscators.io, [^3] many solutions that provide obfuscation protection are available today. Some are free and open-source, and some are paid and commercial (from $199 - 4300).
![the_solution_comparison_of_the_obfuscation_protection.png](/images/protect-dotnet-code-1/the_solution_comparison_of_the_obfuscation_protection.png "The Solution Comparison of the Obfuscation Protection")
*<center>The Solution Comparison of the Obfuscation Protection [^3]</center>*

In the next post, I will introduce the free and open-source solution of obfuscation protection I use to protect the application made by the .NET MAUI framework.

## Reference
[^1]: Microsoft. (2021). .NET assembly file format. Retrieved from https://web.archive.org/web/20240114213646/https://learn.microsoft.com/en-us/dotnet/standard/assembly/file-format
[^2]: Microsoft. (2023). About Dotfuscator Community & Visual Studio. Retrieved from https://web.archive.org/web/20231218081954/https://learn.microsoft.com/en-us/visualstudio/ide/dotfuscator/?view=vs-2022
[^3]: Obfuscators. (2021). The obfuscation resource. Retrieved from https://web.archive.org/web/20231204022411/https://www.obfuscators.io/
[^4]: PreEmptive. (2024). The Importance of Code Obfuscation for .NET and Android Applications. Retrieved from https://web.archive.org/web/20240406174124/https://www.preemptive.com/blog/importance-code-obfuscation-net-android-applications/