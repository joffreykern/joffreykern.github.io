---
layout: post
title: How to build a .NET Core Project with VS Code ?
date: 2017-06-01
categories: dotnetcore VSCode CLI
---

The goal of this post is to provide a few tips to be able to build a .NET Core Project with Visual Code. In this article, i'm using VS Code 1.12 and .net core 1.1.

## <a name='Summary'></a>Summary

<!-- vscode-markdown-toc -->
* [Summary](#Summary)
* [Goal](#Goal)
* [Create a solution template with the CLI](#CreateasolutiontemplatewiththeCLI)
	* [Getting started with the CLI](#GettingstartedwiththeCLI)
	* [Create our solution and all projects](#Createoursolutionandallprojects)
	* [Add projects references on the Solution file](#AddprojectsreferencesontheSolutionfile)
	* [Add projects references on each projects](#Addprojectsreferencesoneachprojects)
* [Working with Visual Code](#WorkingwithVisualCode)
	* [Visual Code needs extensions](#VisualCodeneedsextensions)
	* [Debugging with Visual Code](#DebuggingwithVisualCode)
	* [Unit testing with Visual Code](#UnittestingwithVisualCode)
* [Conclusion](#Conclusion)

<!-- vscode-markdown-toc-config
	numbering=false
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

## <a name='Goal'></a>Goal

I'm an early adopter of .net core and what I loved on this framework is its opening : 
* I don't need Windows to execute C# & .net core.
* I don't need Windows to build a .net core project.
* And I don't need Visual Studio to develop on a .net core project ! 

So the goal of this article is to build a .net project without Visual Studio. You can do the same process on a Linux or a Mac. 

These days, if you want to create a Solution with .sln file and add projects with .csproj files, I cannot imagine someone doing this himself. Let's have a look at a .sln file : 

```
Microsoft Visual Studio Solution File, Format Version 12.00
# Visual Studio 15
VisualStudioVersion = 15.0.26403.7
MinimumVisualStudioVersion = 10.0.40219.1
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "myProject", "myProject\myProject.csproj", "{29380892-85CD-45A2-8821-62599AF51E1F}"
EndProject
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "myProject.Tests", "myProject.Tests\myProject.Tests.csproj", "{87054F2B-B87B-48BF-80F3-79EAE9325C82}"
EndProject
Global
	GlobalSection(SolutionConfigurationPlatforms) = preSolution
		Debug|Any CPU = Debug|Any CPU
		Release|Any CPU = Release|Any CPU
	EndGlobalSection
	GlobalSection(ProjectConfigurationPlatforms) = postSolution
		{29380892-85CD-45A2-8821-62599AF51E1F}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{29380892-85CD-45A2-8821-62599AF51E1F}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{29380892-85CD-45A2-8821-62599AF51E1F}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{29380892-85CD-45A2-8821-62599AF51E1F}.Release|Any CPU.Build.0 = Release|Any CPU
		{87054F2B-B87B-48BF-80F3-79EAE9325C82}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{87054F2B-B87B-48BF-80F3-79EAE9325C82}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{87054F2B-B87B-48BF-80F3-79EAE9325C82}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{87054F2B-B87B-48BF-80F3-79EAE9325C82}.Release|Any CPU.Build.0 = Release|Any CPU
	EndGlobalSection
	GlobalSection(SolutionProperties) = preSolution
		HideSolutionNode = FALSE
	EndGlobalSection
EndGlobal
```

So my problem was: I want to use .sln to aggregate my .csproj, but how can I used them with VSCode ? And don't tell me : "Well, you open Visual Studio 2017, you add your project, and you close it...". No No No No!!!

The other point of this post is: Can I have the same experience on VSCode ? Intellisense, Debugging, Add Nuget Packages, Run Unit Tests... Is it possible to have thoses features on VSCode ? I'll spoil you but: Yes! 

## <a name='CreateasolutiontemplatewiththeCLI'></a>Create a solution template with the CLI

### <a name='GettingstartedwiththeCLI'></a>Getting started with the CLI 

We want to create a solution with 3 projects : a Web application, a Class Library and an Unit Tests Project

```
\ dotnetcorevscode
    \ dotnetcorevscode.Web
    \ dotnetcorevscode.Library
    \ dotnetcorevscode.Tests
```

A good start for the beginners with a CLI : If you need help, you can use ``--help`` argument to get documentation on the command :

```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet --help
.NET Command Line Tools (1.0.4)
Usage: dotnet [host-options] [command] [arguments] [common-options]

...
``` 

If I want more information about ``dotnet new`` command : 

```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet new --help
Template Instantiation Commands for .NET Core CLI.

Usage: dotnet new [arguments] [options]

Arguments:
  template  The template to instantiate.

Options:
  -l|--list         List templates containing the specified name.
  -lang|--language  Specifies the language of the template to create
  -n|--name         The name for the output being created. If no name is specified, the name of the current directory is used.
  -o|--output       Location to place the generated output.
  -h|--help         Displays help for this command.
  -all|--show-all   Shows all templates


Templates                 Short Name      Language      Tags
----------------------------------------------------------------------
Console Application       console         [C#], F#      Common/Console
Class library             classlib        [C#], F#      Common/Library
Unit Test Project         mstest          [C#], F#      Test/MSTest
xUnit Test Project        xunit           [C#], F#      Test/xUnit
ASP.NET Core Empty        web             [C#]          Web/Empty
ASP.NET Core Web App      mvc             [C#], F#      Web/MVC
ASP.NET Core Web API      webapi          [C#]          Web/WebAPI
Solution File             sln                           Solution

Examples:
    dotnet new mvc --auth None --framework netcoreapp1.1
    dotnet new sln
    dotnet new --help
```

### <a name='Createoursolutionandallprojects'></a>Create our solution and all projects

To create project with dotnet cli, we just have to use the ``new`` command. Start by creating the .sln solution : 

```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet new sln --name dotnetcorevscode
Content generation time: 31,9518 ms
The template "Solution File" created successfully.

joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ ls -l
total 1
-rw-r--r-- 1 joffr 197609 540 May 31 15:37 dotnetcorevscode.sln
```

_Don't open the .sln... No don't_

Here we got our .sln file and let's create our three projects : 

```
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode        
$ dotnet new webapi --name dotnetcorevscode.Web                     
Content generation time: 101,2916 ms                              
The template "ASP.NET Core Web API" created successfully.         
                                                                  
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode        
$ dotnet new classlib --name dotnetcorevscode.Library             
Content generation time: 40,9934 ms                               
The template "Class library" created successfully.                
                                                                  
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode        
$ dotnet new mstest --name dotnetcorevscode.Tests                 
Content generation time: 74,2799 ms                               
The template "Unit Test Project" created successfully.            
                                                                  
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode        
$ ls -l                                                           
total 5                                                           
drwxr-xr-x 1 joffr 197609   0 May 31 15:39 dotnetcorevscode.Web/    
drwxr-xr-x 1 joffr 197609   0 May 31 15:39 dotnetcorevscode.Library/
-rw-r--r-- 1 joffr 197609 540 May 31 15:37 dotnetcorevscode.sln   
drwxr-xr-x 1 joffr 197609   0 May 31 15:40 dotnetcorevscode.Tests/
```

### <a name='AddprojectsreferencesontheSolutionfile'></a>Add projects references on the Solution file

On Visual Studio 2017, you create your project with the GUI ``Right click > Add > Add Project > blabla`` then a project appears and the solution file is updated. The ``dotnet new`` command doesn't update the sln for you, you have to do it yourself. You can check by using ``dotnet build`` command, it'll wont build anything.

Add a project to a solution file, you have to use ``dotnet sln`` command. I will add all projects on my solution files : 

```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet sln dotnetcorevscode.sln add dotnetcorevscode.Web/dotnetcorevscode.Web.csproj
Project `dotnetcorevscode.Web\dotnetcorevscode.Web.csproj` added to the solution.

joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet sln dotnetcorevscode.sln add dotnetcorevscode.Library/dotnetcorevscode.Library.csproj
Project `dotnetcorevscode.Library\dotnetcorevscode.Library.csproj` added to the solution.

joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet sln dotnetcorevscode.sln add dotnetcorevscode.Tests/dotnetcorevscode.Tests.csproj
Project `dotnetcorevscode.Tests\dotnetcorevscode.Tests.csproj` added to the solution.
```

Now my projects are referenced by the solution file. If I run a ``dotnet build``, there will be a lot of errors because I didn't use a ``dotnet restore`` to download all dependencies of my solution.

```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet restore
  Restoring packages for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Web\dotnetcorevscode.Web.csproj...
  Restoring packages for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Library\dotnetcorevscode.Library.csproj...
  Restoring packages for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\dotnetcorevscode.Tests.csproj...
  Generating MSBuild file C:\Projects\dotnetcore-vscode\dotnetcorevscode.Library\obj\dotnetcorevscode.Library.csproj.nuget.g.props.
  Generating MSBuild file C:\Projects\dotnetcore-vscode\dotnetcorevscode.Library\obj\dotnetcorevscode.Library.csproj.nuget.g.targets.
  Writing lock file to disk. Path: C:\Projects\dotnetcore-vscode\dotnetcorevscode.Library\obj\project.assets.json
  Restore completed in 867,29 ms for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Library\dotnetcorevscode.Library.csproj.
  Generating MSBuild file C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\obj\dotnetcorevscode.Tests.csproj.nuget.g.props.
  Generating MSBuild file C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\obj\dotnetcorevscode.Tests.csproj.nuget.g.targets.
  Writing lock file to disk. Path: C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\obj\project.assets.json
  Restore completed in 1,72 sec for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\dotnetcorevscode.Tests.csproj.
  Generating MSBuild file C:\Projects\dotnetcore-vscode\dotnetcorevscode.Web\obj\dotnetcorevscode.Web.csproj.nuget.g.props.
  Generating MSBuild file C:\Projects\dotnetcore-vscode\dotnetcorevscode.Web\obj\dotnetcorevscode.Web.csproj.nuget.g.targets.
  Writing lock file to disk. Path: C:\Projects\dotnetcore-vscode\dotnetcorevscode.Web\obj\project.assets.json
  Restore completed in 2,41 sec for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Web\dotnetcorevscode.Web.csproj.

  NuGet Config files used:
      C:\Users\joffr\AppData\Roaming\NuGet\NuGet.Config
      C:\Program Files (x86)\NuGet\Config\Microsoft.VisualStudio.Offline.config

  Feeds used:
      https://api.nuget.org/v3/index.json
      C:\Program Files (x86)\Microsoft SDKs\NuGetPackages\

joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet build
Microsoft (R) Build Engine version 15.1.1012.6693
Copyright (C) Microsoft Corporation. All rights reserved.

  dotnetcorevscode.Library -> C:\Projects\dotnetcore-vscode\dotnetcorevscode.Library\bin\Debug\netstandard1.4\dotnetcorevscode.Library.dll
  dotnetcorevscode.Tests -> C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\bin\Debug\netcoreapp1.1\dotnetcorevscode.Tests.dll
  dotnetcorevscode.Web -> C:\Projects\dotnetcore-vscode\dotnetcorevscode.Web\bin\Debug\netcoreapp1.1\dotnetcorevscode.Web.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:05.96
```

### <a name='Addprojectsreferencesoneachprojects'></a>Add projects references on each projects

On Visual Studio 2017, if you create a new project, you have to add a reference on it by using ``Right click on a project > Add > Reference > ...`` And on our solution, I have to add few references : 
* dotnetcorevscode.Web needs a reference on dotnetcorevscode.Library
* dotnetcorevscode.Tests needs a reference on dotnetcorevscore.Tests

To add a reference on a project, you have to ``dotnet add`` command : 

```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet add ./dotnetcorevscode.Web/dotnetcorevscode.Web.csproj reference ./dotnetcorevscode.Library/dotnetcorevscode.Library.csproj
Reference `..\dotnetcorevscode.Library\dotnetcorevscode.Library.csproj` added to the project.

joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet add ./dotnetcorevscode.Tests//dotnetcorevscode.Tests.csproj reference ./dotnetcorevscode.Library/dotnetcorevscode.Library.csproj
Reference `..\dotnetcorevscode.Library\dotnetcorevscode.Library.csproj` added to the project.
```

And I can check the existing references on a project : 

```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet list ./dotnetcorevscode.Web/dotnetcorevscode.Web.csproj reference
Project reference(s)
--------------------
..\dotnetcorevscode.Library\dotnetcorevscode.Library.csproj
```

Now the structure of our solution is ready and we'll ``code .`` ! 

## <a name='WorkingwithVisualCode'></a>Working with Visual Code

### <a name='VisualCodeneedsextensions'></a>Visual Code needs extensions

Visual Code is a powerful code editor, but it needs some extensions to be usable in a .NET Core project. Here is a list of extensions you must have : 
* [C# By OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) - Provide Syntax Highlighting, IntelliSense, Debugging, etc...
* [Nuget Package Manager](https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager) - Lets you to add or remove package reference in csproj easily. Let's have a look at this extension, its page shows how to simple add a Nuget Package.
* [C# Extensions](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions) - Add some snippets (ctor, prop, ...) and the template for a Class or an Interface.

I advise to have at least C# By OmniSharp & Nuget Package Manager, because without them, it could be harder to work on a .net core project with VS Code... But it's still possible ! 

### <a name='DebuggingwithVisualCode'></a>Debugging with Visual Code

By default, on the left side of your VSCode, you have a crossed bug representing the Debug panel. On this panel, there is everything you need to debug a .net core project : 
* Variables contain current values of your variables when the debugger hits a breakpoint
* Watch allows the developer to have a look at specific variable and change its value
* CallStack redraws the path of your execution

If you hit F5 or press the play button, VSCode will ask you to configure the project : 
![vscode hit F5](/assets/images/blog/201706-dotnet-core-vscode/vscode-hit-f5.png)

Because I have installed C# By OmniSharp, there is .NET Core in the list of available environments. Select .NET Core and a launch.json file will be created in .vscode folder. There are three nodes on ``configurations`` : 
* **.NET Core Launch (console)** to run your project as console.
* **.NET Core Launch (web)** to run your project as web application.
* **.NET Core Attach** to attach an instance of your application on the debugger.

This file is used by VSCode to configure your debugger. There is IntelliSense to add few properties  and you can find more documentation [here](https://code.visualstudio.com/docs/editor/debugging).

On my configuration, I update the **program** property to set the StartUp project from ``"program": "${workspaceRoot}/bin/Debug/<target-framework>/<project-name.dll>",`` to ``"program": "${workspaceRoot}/dotnetcorevscode.Web/bin/Debug/netcoreapp1.1/dotnetcorevscode.Web.dll",``.

If I hit F5 one more time, I'll have an Error : "Could not find the preLaunchTask 'build'." You can create this one by clicking on "Configure Task Runner" and select .NET Core once again. It will create a launch.json file with the ``dotnet build`` event.

_When you run you project you'll have an exception because appsettings.json is not found. You can update the path or move the file on the root of your project_

Finally, on Debug Console : 

```
Hosting environment: Development
Content root path: c:\Projects\dotnetcore-vscode
Launching browser (cmd.exe /C start http://localhost:5000)
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

### <a name='UnittestingwithVisualCode'></a>Unit testing with Visual Code

_N.B. : While writing this post, there were some difficulties to run unit test in VSCode, but the OmniSharp team works well and since the 25th May, we got a little feature on OmniSharp 1.10 : `MSTest support added`_

To run a single test on your .net project, it's very simple on VSCode, go on an UnitTest Class, and click on ``run test`` : 

![Run tests](/assets/images/blog/201706-dotnet-core-vscode/vscode-run-tests.png)

This part is interesting because you can run & debug a single test. Unfortunately, you can't run the tests of a whole class or project... But you still have the CLI : 

Run a complete project : 
```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet test dotnetcorevscode.Tests/dotnetcorevscode.Tests.csproj
Build started, please wait...
Build completed.

Test run for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\bin\Debug\netcoreapp1.1\dotnetcorevscode.Tests.dll(.NETCoreApp,Version=v1.1)
Microsoft (R) Test Execution Command Line Tool Version 15.0.0.0
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...

Total tests: 3. Passed: 3. Failed: 0. Skipped: 0.
Test Run Successful.
Test execution time: 1,1147 Seconds
```

Run a single class : 
```cli
joffr@JOFFREY-LAPTOP MINGW64 /c/Projects/dotnetcore-vscode
$ dotnet test dotnetcorevscode.Tests/dotnetcorevscode.Tests.csproj --filter "FullyQualifiedName~dotnetcorevscode.Tests.UnitTest1"
Build started, please wait...
Build completed.

Test run for C:\Projects\dotnetcore-vscode\dotnetcorevscode.Tests\bin\Debug\netcoreapp1.1\dotnetcorevscode.Tests.dll(.NETCoreApp,Version=v1.1)
Microsoft (R) Test Execution Command Line Tool Version 15.0.0.0
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...

Total tests: 2. Passed: 2. Failed: 0. Skipped: 0.
Test Run Successful.
Test execution time: 1,0783 Seconds
```

## <a name='Conclusion'></a>Conclusion

With this post, you are able to create a .net core project without Visual Studio 2017. There is few bugs on VSCode with the intellisense and few things are missing if I compare VSCode and VS'17, but well, it possible to build a .net core project in VSCode ! 

I encourage you to try this editor, this is a very good editor (I used to build a whole Angular2 project and my VSCode extension). 

_If you followed the entire post, you can try to open your project on a VS'17, it'll works !_ 