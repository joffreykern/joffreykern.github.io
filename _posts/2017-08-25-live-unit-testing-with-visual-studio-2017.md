---
layout: post
title: Live unit testing with Visual Studio 2017
date: 2017-06-01
categories: dotnetcore tdd visualstudio2017
---

.Net core 2.0 is out and one of the most existing feature is : Live Unit Testing ! Let's try that's ! 

## <a name='Summary'></a>Summary

<!-- vscode-markdown-toc -->
* [Summary](#Summary)
* [TL;TR](#TLTR)
* [What do we need](#Whatdoweneed)
* [Live Unit Testing ?](#LiveUnitTesting)
* [Getting started](#Gettingstarted)
* [Is it usable on a real project ?](#Isitusableonarealproject)
* [When I change a single line of code, will it runs all tests ?](#WhenIchangeasinglelineofcodewillitrunsalltests)
* [Can I exclude tests projects which Live Unit Testing ?](#CanIexcludetestsprojectswhichLiveUnitTesting)
* [Going Futher](#GoingFuther)

<!-- vscode-markdown-toc-config
	numbering=false
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

## <a name='TLTR'></a>TL;TR

Before starting anything, I just want to share one thing. I searched on my Visual Studio 2017 Community Edition for at least 30 minutes how to enable this feature... I'll save your time : **It doesn't exist on Community Edition, you need an Enterprise Edition !**

## <a name='Whatdoweneed'></a>What do we need 

To enable the feature "live unit testing" on Visual Studio 2017, we'll need to have : 

* [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)
* Visual Studio 2017 Enterprise Edition with update 15.3

## <a name='LiveUnitTesting'></a>Live Unit Testing ?

The principle is simple : write code and all your tests who needs to be run will be. There is no more break between code write and test run. The concept is intersting today if you are sensitive to TDD, Extreme Programming, etc... 

![gif](https://msdnshared.blob.core.windows.net/media/2017/08/NETCore-Live-Unit-Testing.gif)
_Demonstration from Visual Studio Blog_

The feature works with three test frameworks : MSUnit, xUnit & NUnit.

## <a name='Gettingstarted'></a>Getting started

To start "Live Unit Testing", go to ``Test > Live Unit Testing > Start``. Create a class, write a test. 

The "color's code" is simple : 
* A Green check, your line is tested, and it works ! 
* A Red Cross, your line is tested, and at least one test doesn't work.
* A Bleu dash, your line is not tested.

When you change a single line of your code and save the file, associated unit tests are run. We saw the result on the left side of your editor, and you can have details to know what tests failed and why : 

![Unit tests failed](/assets/images/blog/WIP/red-cross-details.png)

The feature is pretty simple and my IDE stay smooth.

## <a name='Isitusableonarealproject'></a>Is it usable on a real project ?

On my work, our project has more than 3000 unit tests and we have some integrations tests write in C#. As a tester, I need to check few thinks : 
* When I change a single line of code, will it runs all tests ?
* Can I exclude tests projects which Live Unit Testing ?

I'll try to answer on thoses questions.  

## <a name='WhenIchangeasinglelineofcodewillitrunsalltests'></a>When I change a single line of code, will it runs all tests ?

I'll made three class
* `MotherClass` use in its implementation `ChildClass`. Thoses classes are tested by `MotherClassTest`
* `FatherClass` don't use an other class. This class is tested by `FatherClassTest`.
* `CousinClass` don't use an other class. This class is not tested... 

What we expect : 
* When `MotherClass` or `ChildClass` change, `MotherClassTest` run.
* When `FatherClass` change, `FatherClassTest` run. 
* In other class, nothing should be run.

During my tests, I assume that's we got what we expected. In `Windows > Output`, you can select "Live Unit Testing" in `Show output from :` dropdown list. Here what we got : 

```
// I change and save MotherClass or ChildClass, there is two tests on MotherClassTest
[20:48:11.279 Info] Build completed (succeeded).
[20:48:11.287 Info] Started executing 2 tests.
[20:48:12.633 Info] Finished executing tests. (Passed: 2, Failed: 0, Skipped: 0, Not Executed: 0)
// I change and save CousinTest, there is no tests
[20:48:27.417 Info] Build completed (succeeded).
// I change and save FatherTess, there is one test in FatherClassTest
[20:48:35.528 Info] Build completed (succeeded).
[20:48:35.536 Info] Started executing 1 tests.
[20:48:36.652 Info] Finished executing tests. (Passed: 1, Failed: 0, Skipped: 0, Not Executed: 0)
```

As we can see, when a change is made on your code, only tests who test this code will be run! If you want to try yourself : You can have a look on the code [here](WIP).

## <a name='CanIexcludetestsprojectswhichLiveUnitTesting'></a>Can I exclude tests projects which Live Unit Testing ?

## <a name='GoingFuther'></a>Going Futher

The official documentation is [here](https://docs.microsoft.com/en-us/visualstudio/test/live-unit-testing).