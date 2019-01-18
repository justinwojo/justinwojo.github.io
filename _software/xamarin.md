---
title:  "What is Xamarin"
excerpt: "This blog talks about Xamarin, what it is, and why I like it!"
classes: wide
---
Xamarin is a cross-platform framework owned by Microsoft utilizing the C# coding language. The framework allows code sharing between Android, iOS, MacOS and Windows applications. Xamarin was originally created by the company that created Mono, but was purchased by Microsoft in 2016.

Check it out on github: [Xamarin's GitHub](https://github.com/xamarin) 

# Why use Xamarin?
### Code Sharing
With a properly structured codebase, you could share 75% or more of your codebase across iOS and Android (as well as Windows). This means you're only writing business logic once, not twice. This also means you're only testing business logic once as well. Even better is that Xamarin supports [.NET Standard](https://docs.microsoft.com/en-us/dotnet/standard/net-standard), so you can write a library built against .NET Standard that can run on Xamarin as well as your other .NET applications. You can take this a step further by using [Xamarin.Forms](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/) or an mvvm framework like [MvvmCross](https://www.mvvmcross.com/) to share even more code. I'll discuss MvvmCross a bit more below, and in another dedicated post.

### C# Language
I'm a sucker for the C# coding language. It's aesthetically pleasing, powerful, and pretty easy to learn. If you have existing .NET developers, then picking up Xamarin should be significantly easier than them trying to learn Swift, Java, Kotlin, or some other mobile language.

### Amazing IDE (Visual Studio and Rider)
Visual Studio is arguably one of the best IDE's out there. It makes for a fantastic development experience, with tons of extensions to boost productivity. There's also Visual Studio for Mac available that works extremely well to develop Xamarin applications from a Mac. If you don't like Visual Studio, the awesome people over at JetBrains created a cross-platform IDE called [Rider](https://www.jetbrains.com/rider/) that supports Xamarin development as well, with tons of awesome tools, [ReSharper](https://www.jetbrains.com/resharper/) built in for on-the-fly code quality analysis, code standard enforcements and various quality of life improvements.

### Native Controls, Native API Access, Native Performance
One thing that bugs me being a Xamarin developer is that people complain about Xamarin not being native. I get it, I'm not writing Swift or Kotlin, but Xamarin compiles the source code into native assembly code (iOS being Ahead-of-Time compilation and Android being Just-in-Time compilation). This means that Xamarin apps will work just as fast as a native application, have access to all of the native API's (camera, fingerprint scanners, etc.), and use all of the same native controls like Date pickers, Buttons, and others. One quirk is that the Xamarin assemblies needs to be bundled with the .IPA and .APK files, resulting in a larger application size.

### MvvmCross
[MvvmCross](https://www.mvvmcross.com/) is an MVVM (Model-View-ViewModel) framework that allows even more code sharing between platforms, and reduces the overall amount of boilerplate code that would be necessary in a typical Xamarin application built with a pattern like MVC (Model-View-Controller). The whole goal is basically to bind an element on the screen with a property in a viewmodel class in code. Whenever code updates some property, the UI automagically knows that the value changes and updates the UI. Without a framework like this in place, you'd be writing code on iOS and Android independently to update the UI. Also built into the MvvmCross framework is a full cross-platform navigation system. No longer do you need to directly work with the Fragment Manager on Android or the UINavigationController on iOS. MvvmCross's navigation allows you to navigate from one viewmodel to the next inside of your .NET Standard (or PCL) library without any platform specific navigation code. There are some quirks around this where you need to expand upon the built-in functionality and modify the view stack directly on each platform, but for most use-cases the functionality exists already. I'll be writing a more in-depth blog on MvvmCross with code examples.

### Cost
Nothing! Xamarin is completely free to use and open source. One thing worth noting here though is that Visual Studio can cost money if opting for Visual Studio Professional instead of the community edition. There are some limitations when using the Community edition, so it's worth investigating around this if it's a concern.

# Downsides of Xamarin
### Delay of SDK's
Whenever Apple or Google provide some new API's, Xamarin needs to create their own SDK's on top of those. As of recently, they've gotten pretty quick at supporting new SDK's. In the past this wasn't always the case.

### Native UI Building Tools
Android Studio and XCode have some pretty nice tooling built in to build UI's on each platform. Android Studio has the Android Designer, where XCode has the interface builder. Xamarin attempts to build some of this functionality into Visual Studio, but I've always found them slow, painful to use, and tend to have some glitches.

### Issues and Bugs
Once in a while you'll run into some framework issues that cause some pain points when developing. Some of these might be framework related, SDK's, or other general issues. There's a pretty good community and bug system, but it's something you need to be prepared for. As of recently with Microsoft's purchase of Xamarin, the quality has improved significantly around the releases. In my past it was pretty common to be scared to update to a new version of the Xamarin SDK's, but now I tend not to worry and rarely run into issues.

# Conclusion
Overall Xamarin is a great tool. It won't be a perfect fit for every person, team, or organization, but I think it's worth investigating as a solution to build your mobile apps.
