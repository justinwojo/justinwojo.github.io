---
title: "Building a Swift + ObjC .NET Binding Tool Entirely with AI in 6 Weeks"
description: "How I leveraged Claude Code, Codex, and AI pair programming to build a fully functional Swift/C# interop tool from an experimental repo"
date: 2026-03-10
tags: ["software"]
image: "/images/softwareblogs.jpg"
---

If you're reading this, you're probably already asking yourself "is this article AI generated"? Maybe to your surprise, no, every single word in this article is hand-written. Not even an AI grammar/correctness check. However, I cannot say the same about [swift-dotnet-bindings](https://github.com/justinwojo/swift-dotnet-bindings). Every single line of code contributed to this since the fork is 100% written by AI tooling.

You might also be immediately thinking "oh great, another vibe coded tool", and you'd probably usually be right. However, having worked with .Net mobile dev for over a decade, I feel confident in the direction and capabilities of this tool, as I directly steered the ship to make sure we got to the right destination. The ship is a bit damaged from the sea, but nothing that we can't patch up before the next expedition.

Before I jump into my workflow and how I accomplished taking an experimental repo from an idea to a fully functional Swift/C# interop tool AND also a full replacement for objective sharpie in just 6 weeks time as an individual developer, I want to jump into the why.

## The Why

As time has gone on, making bindings has gotten increasingly more difficult, due to lack of tooling updates, poor documentation, broken tools, Swift adoption increasing, etc. One thing that has made making bindings easier is using LLMs like Claude and Codex to help generate and validate the bindings. You can have Claude make a skill that automatically generates bindings for you, and it can work pretty well. So then you may ask, why even bother going through all of this effort to make a tool if Claude/Codex can just make a binding for you? Simple, I'd rather leverage AI to build the right tool, than to have AI wrap duct tape around existing tooling to make it work. It's not sustainable, and not only that, who wants to go through the hassle to work with .NET iOS/MAUI if the only way to use native iOS libraries is to do this song-and-dance of working with dated or straight up broken tools, and rely on Claude/Codex to make a binding work?

That's what drove me to this place to build this tool. I've created dozens of bindings over the years, and it remains a major pain point of working with .NET iOS/MAUI. Even Microsoft can't even bother to upkeep their own bindings (cough, cough, [GooglePlayServicesComponents](https://github.com/xamarin/GooglePlayServicesComponents)). Thankfully people in the community jump in to keep these things going (shout out Adam Essenmacher's [Forked Version](https://github.com/AdamEssenmacher/GoogleApisForiOSComponents)).

It baffles me that Microsoft would invest so heavily in .NET MAUI, and then just completely skip over the fact that having access to these critical bindings and tools is key for adoption.

I've also directly witnessed many vendors that historically shipped Xamarin bindings, not even bother to make updated bindings for MAUI. I'm sure for some of these it's purely a usage-based decision, but I would also bet it is largely due to the complexity and time to update and maintain these bindings, especially now with many vendors going pure Swift on iOS.

## The Vision

I always envisioned a tool that lets you take any native library, and it "just works". No manually reviewing [Model] and [Protocol] accuracy, no [Verify] attributes to cleanup, no dealing with making blank interfaces to fix a binding. Just "here's an iOS library, give me a nuget package".

While that may seem like a pipe dream, it shouldn't be. I've not only proven it's possible, I've actually done it with this tool. A single CLI tool call to go from an xcframework to a working nuget package.

One single command to a functional binding!? Chef's Kiss!

![Chef's Kiss](/images/swift-dotnet-bindings/chefskiss.jpg)

While I've seen adoption of .NET MAUI fall, organizations switching to Flutter or native (including all of the prior companies I've worked for), I just want to do my part to see these tools and this community not just survive, but to thrive. Having any iOS native library easily consumable is just one piece of that puzzle.

## How?

It all started one night, after I had worked the entire day (and that week) on making multiple Swift proxy libraries, to then use objective sharpie, which needed workarounds to make work with Xcode 26, to then work around SwiftUI gaps when bridging to Objective-C. I sat there going, there has to be a better way forward. Anyone picking a mobile app toolset wouldn't touch .NET MAUI with a 10 foot pole if they understood the pain of working with Swift libraries and SwiftUI. Not to say .NET MAUI is all bad, but this particular piece can be so ridiculously frustrating, and the existing tools are all dated and broken.

So, I stumbled across Microsoft's experimental [swift-bindings repo](https://github.com/dotnet/runtimelab/tree/feature/swift-bindings) (which, sadly, hasn't been touched in almost a year), and thought "I wonder how far I could take this with Claude". I was already heavily using Claude Code and other AI tools to write mobile apps, and figured why not see how far I could get if I toss it at a fork of this repo?

### The First Win

I started with one simple task, make Nuke image loading work end-to-end via this tooling, and make a binding I could actually use. All existing bindings of Nuke today require Swift proxies, and only expose a tiny fraction of the binding, as it's a lot of work to expose everything (lots of extra Swift code in the proxy to make it work well with the Objective-C bridge).

Within a couple of days, I had a working prototype. I had Claude make a simulator test app that actually used the generated binding (direct Swift to C# interop), and it was working! I was loading images and displaying them all through this interop. That was the key win that kept me going down this path. It proved the idea was real and it could work.

After this, the next library that felt right to me was Lottie. A library I've used a ton in the past, but is now all Swift and SwiftUI based. I kept having Claude generate the binding, analyze what was broken, and fix. This loop went on for days and days. Before long, it mostly worked, but it left the code in somewhat of a mess.

### The LLM Swarm

Enter what I like to call [AI Pair Programming](https://github.com/justinwojo/claude-skills?tab=readme-ov-file#ai-pair-programming). Claude does great work, but like any seasoned engineer, we all make mistakes. I couldn't keep up with the code, and it was clearly making mistakes. So, I generated this skill so after each session of work, Claude could consult with ChatGPT, Gemini, and/or Grok (all 3 when it made sense). That helped button up a lot of the session work so silly mistakes didn't slip through the cracks.

Not long after this, Codex released their macOS app. I had just started trying Codex via the terminal, but since the codex cli runs commands/scripts on their systems, it couldn't actually run Mac/Xcode dependent scripts. The Codex Mac app fixed this, so it could actually run local commands, run sim tests, etc. I quickly adopted a new workflow where I instead used the Codex Mac app as my pair programmer/review tool (instead of the API calls). Even though this was more manual work on my side (vs it being automatic with the API), I found that the feedback the codex app provided was almost always valid and accurate feedback, whereas the APIs would hallucinate. This all comes down to context. The pair programming skill simply calls the APIs with limited context "hey, review these few files/diff, let me know of any issues". Works fine, but it doesn't understand the entire repository, it can't grep/search, it can't run scripts, etc. So, often the feedback was mixed if it was valid or not.

### The Workflow

For the last month of work on this project, the key workflow for me was Claude Plan -> Codex Plan Review -> Claude Implement -> Codex Code Review.

I found that this was the best balance of speed and accuracy. I used Opus 4.5 and then Opus 4.6 for all of the work, and then GPT 5.2 Codex, into GPT 5.3 Codex, and now onto GPT 5.4.

### Managing the Work

As far as managing the work, I decided to opt for markdown based files. I organized this into 3 simple directories:

- `src/docs/` for the active work/roadmaps
- `src/docs/future` for things I wanted to do eventually, but not actively prioritized (essentially my backlog)
- `src/docs/completed` for finished work. Arguably not needed, but nice to be able to look back if needed versus just deleting them on completion.

I broke up work into roadmap markdown files. All work was organized by priority, and also by workable sessions. Claude managed all of the work as well, I just steered the priority and what to build. Each session was designed to try accomplish all of the work in a single context window without auto-compaction. It didn't always work, but that was the ideal flow. Claude has improved quite a bit with Opus 4.6 (vs 4.5) on managing larger context and hallucinating less, so it's not as critical now as it was on Opus 4.5. Before, it was ideal to keep any given session within a 120k context window for best results.

That's basically it from a workflow perspective. I really emphasized trial-and-error in this whole approach. Add a new binding library, try it, fix, repeat. Once a library succeeded, find another one to try.

## Testing and Validation

Along the way, I always tried to ensure this project had proper test infrastructure. As of this blog writing, over 6000 unit tests are written, 700+ integration tests, and a full test framework that takes a bunch of Swift paradigms, generates a binding, and then tests it on simulators.

It wasn't long before I realized that it's almost impossible to test all of the crazy patterns that real-life libraries exercise, so I created a validation script that actually runs the binding generation across ~46 different real libraries (90 different individual libraries with inter-dependencies), and flags any regressions as we improve the binding generation tooling. This became a critical workflow piece, as it became pretty common that a fix for one library would cause downstream failures in other libraries.

## Bridging the Type Gap

I also realized that trying to bridge the Objective-C types that .NET surfaces with the Swift types was becoming a serious developer experience bottleneck. The first few libraries that this tool generated were, to put it bluntly, ugly. Things like SwiftString, SwiftBool, SwiftOptional were all over the generated binding. Lots of leaked types that you couldn't actually use (and shouldn't see), etc. While you could technically use it and make it work, I never would have shipped it in this state. Over time with many iterations, we hid all of the Swift types and used built-in C# types and also automatically converted to existing Objective-C types built into .Net so you didn't need to learn new types.

For example, CoreGraphics.CGRect is a type you've probably used. The binding tool generated it under a Swift namespace, resulting in Swift.CGRect, and all methods you called required this namespace, and you couldn't use CoreGraphics.CGRect, even though they were technically the same in memory. Many things like these had to be fixed and bridged so you didn't need to re-learn or use these new types vs the existing built-in ones. It's not perfect, I'm sure there are some that are missed, but the majority of them are proper.

## Tossing in the Towel (Almost)

As we added more and more Swift features, it quickly became a bit of an unstable mess. The binding would get generated fine, we'd run it on a simulator, and it'd crash. Claude would dig into it, make a determination as to what the issue is, make a fix, and it'd work. Then I'd try run that exact same binding and code on my iPhone, and it'd crash. This was a pretty familiar loop for a long time, to the point where I was convinced that this entire repo was just going to get thrown away due to the instability.

Before we dive in, there's one key component to understand, which is [CallConvSwift](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.compilerservices.callconvswift?view=net-9.0). In a nutshell, this .NET 9 addition allows C# to call P/Invoke directly into Swift functions using Swift's native ABI.

In .Net iOS (or MAUI on iOS), when you're running your app on a simulator, the app will run using Mono JIT. When you run it on a physical iPhone, it'll run with NativeAOT. The key thing to understand here is the way that P/Invoke calls happen are fundamentally different on each. This can lead to a situation where CallConvSwift translation works in Mono JIT and not in NativeAOT, or vice versa.

After a while, Claude made a determination that there was a Mono JIT bug where it classified P/Invoke frames as async when they're not, which affected a significant amount of features. After digging into it, we determined we could "fix" it by building a workaround just for simulators, and it technically worked. However, this wasn't the only issue, there were more. Same flow, Claude determined it was a Mono JIT issue with CallConvSwift, add another lengthy workaround, proceed. A very similar situation happened with NativeAOT, Claude determined there were bugs, find "fixes" (workarounds), repeat.

I can't say how many crashes later, but I finally lost all confidence in what we were building. I finally had to take a step back, and rethink our direction.

### The Pivot

After some retrospective of the "fixes" Claude was making, it determined that using @_cdecl (a Swift compiler attribute) was bulletproof. Every time we used it, it just worked. The compiler attribute, @_cdecl, exports Swift functions with C calling conventions with a stable symbol name. The important part is those functions are compiled by Swift. So after some digging, we decided it was time (at least temporarily), to bypass CallConvSwift in the majority of cases and opt for @_cdecl anywhere we could. The CallConvSwift layer remains, so we could switch back in the future.

The downsides of cdecl compared to CallConvSwift is an additional Swift compilation step and an extra function call.

CallConvSwift Route:  `C# → [CallConvSwift] → Swift ABI directly → crashes sometimes`

CDecl Route:   `C# → [Cdecl] → @_cdecl wrapper → Swift ABI internally → always works`

After a week of migrating, with roughly 80% of all binding code using cdecl instead of CallConvSwift, we were still running into crashes, and instability. In almost every case, similar to before, Claude would find a fix, and eventually we had some level of stability in quite a few binding libraries.

### When Trusting Claude Goes Wrong

I had Claude maintain various bug reports for the Mono JIT and NativeAOT issues, so I could file them once the repo was public. When you submit issues, they often request for a small sample repo to reproduce the issues. I of course had this binding repo to share, but it had too many moving pieces, and I wanted to keep it simple, so I had Claude create a minimal repo with reproduction of the bugs.

Here's where I started to question.....everything. Claude wrote up a simple test app, and it started with the Mono JIT P/Invoke async issue I mentioned above. To say what happened next was unexpected would be an understatement. I simply read the words that Claude so nicely bolded for me "***This is enlightening!***". It worked!? Certainly Claude messed up the test, right? After all, it was so confident in the original diagnosis, even after dozens of sessions running into the crashes caused by it and making workarounds.

Sure enough, everything we thought we knew, we didn't. Each issue it tried to reproduce in this test app, passed with flying colors. Almost every Mono JIT issue worked. I then had it do the same tests for NativeAOT, and sure enough, most things passed.

All of that time, all of those sessions, all of those crashes, and the workarounds, wasted. 

Rarely do I ever feel defeated, but it certainly felt like it for a bit.

### What Now?

After having Claude triple check its findings, it was confident that almost every single issue was in our binding generation. While this was painful, it also meant there was a light at the end of the tunnel. Being held up by Microsoft was a worst-case scenario, and this meant that we might not be anymore. It meant there was a path where I could deliver a stable binding tool, something I wasn't sure I could do before this point.

As part of this reproduction testing, we did discover a few bugs in NativeAOT and Mono JIT though, but they were completely different from what Claude assumed originally. On the bright side, the workarounds for these bugs were to use cdecl, which we already had done the work for, so not everything was throwaway from that diversion. 

The takeaway from all of this, as cliché as the saying is, sometimes you need to slow down to speed up. Instead of going with the narrative, I should have stopped, and really honed in on the original issue instead of trusting the initial gut instinct.

At the end of the day, we learn, we improve, we move on.

## Why Bother Replacing Objective Sharpie?

The intention of this project originally was pure Swift libraries. Objective Sharpie and ObjC bindings have existed for over a decade, and they've worked. I never planned on or envisioned doing anything with Objective-C libraries in this repo. However, a few things were discovered over the course of this project:

1. Many libraries today intermix Swift and Objective-C. How can you bind a Swift library, that has a dependency on an Objective-C library, and make all the types map correctly?
2. Many major libraries today are still using Objective-C, and while people (like Adam E from above) are maintaining bindings, they're still difficult to update and maintain with the existing tooling.

My goal was already "xcframework to nuget package, no manual intervention" with Swift libraries, and the tooling that was built to accomplish that actually became extremely valuable in adding Objective-C binding support as well. So, within just a day or two of work, we were able to not only fully integrate Objective-C bindings, but also to significantly improve the generated bindings from what Objective Sharpie does today. I had Claude compare bindings generated by Objective Sharpie to our own tooling, and went back-and-forth to ensure that our tooling met or exceeded it in every single way. Not only that, but it works with modern Xcode, uses built-in tooling (no extra downloads), and also generates the whole project for you.

This also meant that working with mixed libraries became possible. Libraries like Stripe intermix Swift and Objective-C, and I wanted this project to work with advanced use cases like Stripe. Again, my goal was "any xcframework to nuget package", and if it couldn't handle Stripe's 14 libraries with mixed Swift + ObjC, I didn't want to ship it.

## Swift Package Manager

Another huge hurdle was Swift Package Manager. It was always my intention to bake in SPM support directly into this tool, and while it's technically possible, I decided to separate the tools. I created a [standalone script](https://github.com/justinwojo/spm-to-xcframework) that fully handles taking any SPM link and turning it into ready-to-go xcframework files to feed into the binding tool. Swift, Objective-C, mixed, dynamic, static, anything. One of the critical pieces of this binding tool is it needs to be a dynamic library and generated with the proper flags in order to create the binding, and this script handles all of that for you.

Naturally, I also created a skill to use with Claude, Codex, or any other LLM tool that can do tool calling, to do all of this work for you. It'll take any local xcframework or SPM link, make the appropriate packages, feed it into the binding generation tool, and even can run validation for you on the resulting binding (or help troubleshoot any binding issues). Again, with the goal of making consuming native iOS libraries on .Net iOS/MAUI dead simple.

## Package Repo + Samples

As part of this, I also wanted to contribute to the community by providing a repo that houses bindings to popular libraries (like Nuke and Lottie), while also providing samples, and tools/scripts that can be helpful for managing your own bindings. So, I created [swift-dotnet-packages](https://github.com/justinwojo/swift-dotnet-packages), which provides a templating system to make adding new bindings extremely easy, simulator binding testing infrastructure, and a sample app demonstrating using the bindings in a real app.

It's a work in progress, and I'll continue to improve this repo, as well as all of these tools.

## What's Next?

Continuing to improve the [swift-dotnet-bindings](https://github.com/justinwojo/swift-dotnet-bindings) repo is my main focus. There are plenty of edge cases that'll be uncovered as people use it, issues when actually running the binding on devices, etc. I want to remain available to swarm on any new issues.

Beyond this, I might consider some tooling around Android bindings, but I feel like those aren't nearly as painful as iOS bindings, so no promises 😊.

I have plenty of other ideas that I'd love to explore, both in the .Net mobile space and elsewhere, so follow along if these types of projects interest you. I'll try stay on top of writing more posts, and contributing to the .Net mobile ecosystem where I can.

If you made it this far, thanks for the support on these tools. Xamarin/.Net has given me a great career, and I'm thankful for the opportunities it's provided me and my family. It felt only right to me to create these tools under MIT and open source as a thanks to this community. This project was never about money, it was about keeping alive a great community, and I hope it benefits people as much as I think it will.