+++
title = 'Code Obfuscation for .NET MAUI (Target to Windows) Cont.'
summary = 'How to security your MAUI app to defends from reverse engineering'
description = 'How to security your MAUI app to defends from reverse engineering'
date = 2024-04-11T00:10:00+08:00
series = [".NET", "Security"]
weight = 1
aliases = ["/protect-dotnet-code-3"]
tags = [".NET", ".NET MAUI", "Security", "Reverse Engineering", "Obfuscation", "BitMono"]
author = ["Tim"]
draft = false
ShowToc = true
ShowReadingTime = true
+++

## Merge the obfuscate process into the publishing flow
Understanding the fundamentals of [MSBuild](https://learn.microsoft.com/en-us/visualstudio/msbuild/common-msbuild-project-properties?view=vs-2022) [^1] is good for comprehending the merging of the obfuscate process into the publishing flow. I don't want to talk about MSBuild in the post (But I added some comments for you to catch the action briefly), so just copy the following code into the `<Project>` element in the `.csproj` file is good to go.

```
<Target Name="BitMono" AfterTargets="AfterBuild" Condition="'$(Configuration)'=='Release' AND $(TargetFramework.Contains('windows')) == true">
    <ItemGroup>
        <Sourcelibs Include="$(TargetDir)**\*.*" />
    </ItemGroup>

    <!-- Make folder for BitMono reference -->
    <ItemGroup>
        <BitMonoReference Include="$(TargetDir)libs" />
    </ItemGroup>
    <MakeDir Directories="@(BitMonoReference)" />

    <!-- Copy all library files from Sourcelibs folder to BitMonoReference folder -->
    <Copy SourceFiles="@(Sourcelibs)" DestinationFolder="@(BitMonoReference)" />

    <!-- Execute obfuscation -->
    <Exec WorkingDirectory="$(TargetDir)" Command="BitMono.CLI -f $(ProjectName).dll" />

    <!-- Once BitMono finish the work, remove the BitMonoReference folder -->
    <RemoveDir Directories="@(BitMonoReference)" />

    <ItemGroup>
        <BitMonoOutput Include="$(TargetDir)output\*.*" />
    </ItemGroup>

    <!-- Copy the output files generate by BitMono to TargetDir -->
    <Copy SourceFiles="@(BitMonoOutput)" DestinationFolder="$(TargetDir)" />
</Target>
```

## Publish the Windows app

### Publish .NET MAUI Windows App with MSIX format
1. Set the Configuration as `Release` and `Any CPU`
   ![msix_build_0.png](/images/protect-dotnet-code-3/msix_build_0.png "Publish MSIX Step 1")
   *Publish MSIX Step 1*

2. Open the Solution Explorer and right-click the project and click `Publish` in the selection list
   ![msix_build_1_0.png](/images/protect-dotnet-code-3/msix_build_1_0.png "Publish MSIX Step 2")
   *Publish MSIX Step 2*

3. Select `Microsoft Store under a new app name` selection and click `Next`
   ![msix_build_1_1.png](/images/protect-dotnet-code-3/msix_build_1_1.png "Publish MSIX Step 3")
   *Publish MSIX Step 3*

4. Select the app name (You should create and register the app name on the [Microsoft Partner Center](https://partner.microsoft.com/en-us/dashboard/account/exp/enrollment/welcome?cloudInstance=Global&accountProgram=valueaddedreseller) first) and click `Next`
   ![msix_build_1_2.png](/images/protect-dotnet-code-3/msix_build_1_2.png "Publish MSIX Step 4")
   *Publish MSIX Step 4*

5. Set up the version number and create the publishing profile. **Please be sure the publishing profile is related to the "Release" setting** (You can refer to the following image for more details).
   ![msix_build_2.png](/images/protect-dotnet-code-3/msix_build_2.png "Publish MSIX Step 5")
   ![msix_build_3.png](/images/protect-dotnet-code-3/msix_build_3.png "Publish MSIX Step 6")
   *Publish MSIX Step 5 & 6*

6. Once finish the setup, click `OK` and `Create`

### Publish .NET MAUI Windows App Unpackaged
Publishing the .NET MAUI Windows App unpackaged instead of MSIX.

1. Modify the `launchSettings.json` (In `Properties` folder in MAUI project) as the following:

```
{
  "profiles": {
    "Windows Machine": {
      // Don't use MSIX, Comment the following line
      // "commandName": "MsixPackage",
      "commandName": "Project",
      "nativeDebugging": false
    }
  }
}
```

2. Add the following code into the `<PropertyGroup>` element in `.csproj` file:
   `<WindowsPackageType>None</WindowsPackageType>`

3. Set the Configuration as `Release` and `Any CPU` and then click the `▶️ Windows Machine` button
   ![unpackage_build.png](/images/protect-dotnet-code-3/unpackage_build.png "Publish .NET MAUI Windows App Unpackaged")
   *Publish .NET MAUI Windows App Unpackaged*

## Discussion
### Obfuscation
In this demo, we **disable all the protection but enable** `StringsEncryption` to demonstrate and discuss the Bitmono Obfuscation.

During the publishing process, Bitmono will run and obfuscate the .NET assembly (dll) after your MAUI project is built successfully. (You can see the obfuscation log in the Build console window in the Visual Studio)
![bitMono_output_1.png](/images/protect-dotnet-code-3/bitMono_output_1.png "BitMono Log (Started)")
*BitMono Log (Started)*

Once Bitmono obfuscates successfully, you will see the log like the following:
![bitMono_output_2.png](/images/protect-dotnet-code-3/bitMono_output_2.png "BitMono Log (Finished)")
*BitMono Log (Finished)*

### Testing the app
Please locate the publishing folder and open the app to confirm that it can launch and its behavior is correct and expected.

#### Packaged app (MSIX)
Please refer to the [Microsoft technical documentation](https://learn.microsoft.com/en-us/dotnet/maui/windows/deployment/publish-cli?view=net-maui-8.0#installing-the-app) to install the packaged app and then open it for testing.

#### Unpackaged app
Open the exe file in the publishing folder. (`MauiBitMono\bin\Release\net8.0-windows10.0.19041.0\win10-x64\MauiBitMono.exe` in this demo)
![exe_path.png](/images/protect-dotnet-code-3/exe_path.png "EXE File")
*Open the App*
![bitMono_output_3.png](/images/protect-dotnet-code-3/bitMono_output_3.png "Confirm Everything is OK with the App")
*Confirm Everything is OK of the App*

#### Troubleshooting
There are many reasons why the app does not launch correctly; one possible is caused by obfuscation. Please use [Windows Reliability Monitor](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc748864(v=ws.10)#additional-considerations) to [identify the cause of software failures](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc749583(v=ws.10)#software-failures). [^2] If the failure is caused by obfuscation, please create an issue from the [BitMono repository](https://github.com/sunnamed434/BitMono/issues) so that the community or maintainer can fix it.

### Verification and Comparison
We can drag and drop the .NET assembly (dll) into the .NET decompiler (such as [ILSpy](https://github.com/icsharpcode/ILSpy)) to verify the code obfuscation (`MauiBitMono\bin\Release\net8.0-windows10.0.19041.0\win10-x64\MauiBitMono.dll` in this demo).
![dll_path.png](/images/protect-dotnet-code-3/dll_path.png "DLL File")
*Decompile the DLL*
![decompile_by_ilspy.png](/images/protect-dotnet-code-3/decompile_by_ilspy.png "Decompile by ILSpy")
*Decompile by ILSpy*

In this demo, we create a simple MAUI project. The original code of function `private void OnCounterClicked(object sender, EventArgs e)` in file `MainPage.xaml.cs` is shown as the following:
```
private void OnCounterClicked(object sender, EventArgs e)
{
    count++;

    if (count == 1)
        CounterBtn.Text = $"Protected by BitMono. Clicked {count} time.";
    else
        CounterBtn.Text = $"Protected by BitMono. Clicked {count} times.";

    SemanticScreenReader.Announce(CounterBtn.Text);
}
```

After obfuscation, the corresponding function code comes from the ILSpy decompiler result is shown as the following. We can see that the string in the code is obfuscated and cannot identify the original text:

```
private void OnCounterClicked(object sender, EventArgs e)

{

    count++;

    if (count == 1)

    {

        Button counterBtn = CounterBtn;

        DefaultInterpolatedStringHandler defaultInterpolatedStringHandler = new DefaultInterpolatedStringHandler(36, 1);

        defaultInterpolatedStringHandler.AppendLiteral(DeleteGroup get_Timeout GetTypesFromInterface.set_TradeBanState.ExecuteDependencyCode GetPlugins FixedUpdate.SetCooldown(global::<Module>.<>c.RemovePlayerFromGroup get_MemberSince checkCommandMappings.GetGroup, global::<Module>.<>c.get_Instance GetOpenWindows IsDependencyLoaded.Start, global::<Module>.<>c.set_AvatarFull get_HoursPlayedLastTwoWeeks ExternalLog.set_MemberSince));

        defaultInterpolatedStringHandler.AppendFormatted(count);

        defaultInterpolatedStringHandler.AppendLiteral(DeleteGroup get_Timeout GetTypesFromInterface.set_TradeBanState.ExecuteDependencyCode GetPlugins FixedUpdate.SetCooldown(global::<Module>.<>c.LoadDefaults get_Help GetTypesFromInterface.Load, global::<Module>.<>c.get_Instance GetOpenWindows IsDependencyLoaded.Start, global::<Module>.<>c.set_AvatarFull get_HoursPlayedLastTwoWeeks ExternalLog.set_MemberSince));

        counterBtn.Text = defaultInterpolatedStringHandler.ToStringAndClear();

    }

    else

    {

        Button counterBtn2 = CounterBtn;

        DefaultInterpolatedStringHandler defaultInterpolatedStringHandler = new DefaultInterpolatedStringHandler(37, 1);

        defaultInterpolatedStringHandler.AppendLiteral(DeleteGroup get_Timeout GetTypesFromInterface.set_TradeBanState.ExecuteDependencyCode GetPlugins FixedUpdate.SetCooldown(global::<Module>.<>c.set_Timeout FixedUpdate LoadPlugin.FireEventCallback, global::<Module>.<>c.get_Instance GetOpenWindows IsDependencyLoaded.Start, global::<Module>.<>c.set_AvatarFull get_HoursPlayedLastTwoWeeks ExternalLog.set_MemberSince));

        defaultInterpolatedStringHandler.AppendFormatted(count);

        defaultInterpolatedStringHandler.AppendLiteral(DeleteGroup get_Timeout GetTypesFromInterface.set_TradeBanState.ExecuteDependencyCode GetPlugins FixedUpdate.SetCooldown(global::<Module>.<>c.LogWarning Save get_ConnectedTime.get_AvatarMedium, global::<Module>.<>c.get_Instance GetOpenWindows IsDependencyLoaded.Start, global::<Module>.<>c.set_AvatarFull get_HoursPlayedLastTwoWeeks ExternalLog.set_MemberSince));

        counterBtn2.Text = defaultInterpolatedStringHandler.ToStringAndClear();

    }

    SemanticScreenReader.Announce(CounterBtn.Text);

}

```

### What BitMono does?
In general, BitMono encrypts strings in code using basic AES encryption. It converts the string to a byte array and then uses the cryptographic services provided by [System.Security.Cryptography](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography?view=net-8.0) to encrypt the byte array. You can see the implementation [here](https://github.com/sunnamed434/BitMono/blob/main/src/BitMono.Protections/StringsEncryption.cs) and [here](https://github.com/sunnamed434/BitMono/blob/main/src/BitMono.Runtime/Encryptor.cs#L5).

## Conclusion
In this tutorial, we [use BitMono to obfuscate](###Obfuscation) the Windows app made with the .NET MAUI framework. We also [merge the obfuscate process](#Merge-the-obfuscate-process-into-the-publishing-flow) into the publishing flow by adding the MSBuild XML code to the `.csproj` project file. In our demo, we enable the `StringsEncryption` protection to [demonstrate the obfuscation](#Obfuscation) and then try to [decompile the obfuscated (protected) .NET assembly](#Verification-and-Comparison). The ILSpy decompile result shows that the string in the code is obfuscated and we cannot identify the original text.

You can enable more protection on your application to decrease the harm from reverse engineering, but again, please be careful of the protection's compatibility and the impact when protection is enabled (such as slowing down the app's performance).

Bear in mind that nothing is uncrackable; only the software on the server side is well-protected and unbreakable generally. Code obfuscation techniques cannot completely eliminate the risk and harm of reverse engineering. Even if the green-hand reverse engineer, just opens the [IDA-Pro](https://en.wikipedia.org/wiki/Interactive_Disassembler) [^3] for example, who can browse your code like a hot knife through butter. Obfuscation ONLY lets them slow down the speed of analyzing and feel pain when trying to analyze the code.

## Download the Demo Example
The demo throughout this tutorial is available on [GitHub](https://github.com/shingming/MauiBitMono). [^4]

## Reference
[^1]: Microsoft. (2024). Common MSBuild project properties. Retrieved from https://web.archive.org/web/20240318130631/https://learn.microsoft.com/en-us/visualstudio/msbuild/common-msbuild-project-properties?view=vs-2022
[^2]: Craig Marcho. (2019). Using Reliability Monitor for Troubleshooting. Retrieved from https://web.archive.org/web/20230524035106/https://techcommunity.microsoft.com/t5/ask-the-performance-team/using-reliability-monitor-for-troubleshooting/ba-p/372962
[^3]: Wikipedia contributor. (2024). The Interactive Disassembler (IDA). Retrieved from https://web.archive.org/web/20240330203125/https://en.wikipedia.org/wiki/Interactive_Disassembler
[^4]: Shing Ming (Tim), Wong. (2024). MauiBitMono - The tutorial on using BitMono obfuscator to protect .NET MAUI Apps. Retrieved from https://github.com/shingming/MauiBitMono
