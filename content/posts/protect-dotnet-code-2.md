+++
title = 'Code Obfuscation for .NET MAUI (Target to Windows)'
summary = 'How to security your MAUI app to defends from reverse engineering'
description = 'How to security your MAUI app to defends from reverse engineering'
date = 2024-04-11T00:00:00+08:00
series = [".NET", "Security"]
weight = 1
aliases = ["/protect-dotnet-code-2"]
tags = [".NET", ".NET MAUI", "Security", "Reverse Engineering", "Obfuscation", "BitMono"]
author = ["Tim"]
draft = false
ShowToc = true
ShowReadingTime = true
+++

## Background
[comment]: <.NET MAUI is the brand-new development framework proposed by Microsft in May 2020. It is a younger framework and technology whose key characteristic is that developers can write native apps and deploy to multiple platforms with one code base using C# language.>

Many open-source obfuscators, such as ConfuserEx [^1] and DotNetPatcher, [^2] are available on GitHub, but most of them are out of support or maintenance. I tried to use the above solution to obfuscate my MAUI project but got some errors during the obfuscation processing. When I struggled with these errors, I found and tried a new obfuscator named BitMono, [^3] which has been developed since 2022. The most important part is that it supports a new version of .NET (6.0 is supported) by comparison and obfuscates the MAUI app without an error.

## Overview
I will not put much effort into the BitMono introduction in this post. In fact, its [GitHub page](https://github.com/sunnamed434/BitMono) already provides details about this so you can browse and see the GitHub page for the introduction. **Instead, this post focuses on how to merge the obfuscate process into the publishing flow of the .NET MAUI app for Windows.** I split these into two posts so each does not seem too long - The first post focuses on environment setup, and the second focuses on merging the obfuscate process.

## BitMono
GitHub page: https://github.com/sunnamed434/BitMono

Capture in Apr. 2024, BitMono's latest version of pre-release as [v0.20.1-alpha.36](https://github.com/sunnamed434/BitMono/releases/tag/v0.20.1-alpha.36) provides 16 protection. You can see about each protection description via BitMono documentation. [^4] This post also arranges the table, which includes the protection name, description, and limitations of BitMono provided. [^5]

## Environment Setup
Test on Windows 11 23H2 x64
1. Install .NET 6 SDK (BitMono requirement)
   2. Download the latest BitMono version on the [GitHub page](https://github.com/sunnamed434/BitMono/releases)
      ![bitmono_download_on_github.png](/images/protect-dotnet-code-2/bitmono_download_on_github.png "Download BitMono on GitHub")
      *Download BitMono on GitHub*
2. Unzip the zip file and add the folder path created by unzipping (call `BitMono folder` in this post) to the `Windows environment variable`
3. In the BitMono folder, open the file named `obfuscation.json`
4. Set the variables `Watermark` and `OpenFileDetinationInFileExplorer` as `false` values

## The strategy of choosing the right protection for your application
**Not every protection is suitable** for a .NET MAUI project (or your project) because some protection is only for .NET Core, and some protection is supported by the Mono framework only. On the other hand, some protection, such as `StringsEncryption`, has a significant impact on the application's performance, [^6] so an application (may) shouldn't be protected by this if it is sensitive to performance. To decide what BitMono protection you should adopt for your .NET MAUI project, please browse and analyze the BitMono documentation and each protection advantage and drawback. [^4]

## Enable/Disable the protection
1. Open the file named `protections.json` in the BitMono folder (folder created by unzipping the BitMono you have downloaded).
2. Edit the protection field `Enabled` as `true` or `false` to enable or disable the corresponding protection.
   ![bitmono_protections.png](/images/protect-dotnet-code-2/bitmono_protections.png "The Protection Configuration")
   *The Protection Configuration*

Once the above steps are finished, we can move on to the next session - Merge the obfuscate process into the publishing flow of the .NET MAUI app for Windows. This will be introduced in the next post.

## Reference
[^1]: Martin Karing et al. (2022). ConfuserEx: An open-source, free protector for .NET applications. Retrieved from https://web.archive.org/web/20240217050812/https://github.com/mkaring/ConfuserEx
[^2]: 3DotDev. (2020). DotNetPatcher: DotNet Obfuscator/Packer. Retrieved from https://web.archive.org/web/20210116232701/https://github.com/3DotDev/DotNetPatcher
[^3]: sunnamed434 et al. (2024). BitMono: Unlock new level of security with BitMono. Retrieved from https://web.archive.org/web/20240407131159/https://github.com/sunnamed434/BitMono
[^4]: sunnamed434 et al. (2024). BitMono — BitMono 0.1 documentation. Retrieved from https://web.archive.org/web/20240407131809/https://bitmono.readthedocs.io/en/latest/index.html
[^5]: [BitMono Obfuscator Feature Table](/posts/bitmono-obfuscator-feature-table)
[^6]: sunnamed434 et al. (2023). StringsEncryption — BitMono 0.1 documentation. Retrieved from https://web.archive.org/web/20240407163929/https://bitmono.readthedocs.io/en/latest/protections/stringsencryption.html