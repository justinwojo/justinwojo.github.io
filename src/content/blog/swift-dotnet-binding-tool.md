---
title: "Swift .NET Bindings: The Objective Sharpie Replacement for .NET MAUI and iOS"
description: "Generate C# bindings from any Swift or Objective-C framework with one command. No proxy libraries, no bridging headers, no [Verify] attributes. The modern binding tool for .NET MAUI and .NET iOS developers."
date: 2026-04-02
tags: ["software"]
image: "/images/swift-dotnet-bindings/cover.jpg"
---

Objective Sharpie is no longer maintained. It doesn't work with modern Xcode without workarounds, and it has zero support for Swift-only frameworks — meaning StoreKit 2, SwiftUI, WeatherKit, Swift Charts, and the growing list of Swift-first Apple APIs are completely out of reach for .NET developers.

If you're building .NET MAUI or .NET iOS apps today and need a native Swift library, your options have been to write fragile proxy libraries by hand, fight with Objective-C bridging headers, or simply go without.

[**swift-dotnet-bindings**](https://github.com/justinwojo/swift-dotnet-bindings) changes that. One command. Any xcframework. Ready-to-use NuGet package.

## What Is swift-dotnet-bindings?

It's an open-source binding generator that takes compiled Swift or Objective-C frameworks (.xcframework) and produces complete, ready-to-compile C# binding projects. No manual intervention, no proxy layers, no bridging headers.

**One command to go from xcframework to NuGet package:**

```bash
# Install the template
dotnet new install SwiftBindings.Templates

# Create a binding project
dotnet new swift-binding -n MyLibrary.Swift.iOS

# Copy your xcframework in, then build + pack
dotnet build
dotnet pack
# → Produces a fully documented NuGet package ready to drop into any .NET MAUI or .NET iOS project
```

That's it. From xcframework to usable NuGet package in minutes, not hours.

## Why Replace Objective Sharpie?

[Objective Sharpie](https://learn.microsoft.com/en-us/xamarin/cross-platform/macios/binding/objective-sharpie/) was the standard tool for creating iOS bindings in Xamarin and early .NET MAUI. But it has serious limitations that make it increasingly impractical:

| | **Objective Sharpie** | **swift-dotnet-bindings** |
|---|---|---|
| **Swift support** | None — requires manual proxy libraries | Full — classes, structs, enums, protocols, generics, async, closures |
| **Output quality** | Littered with `[Verify]` attributes requiring manual cleanup | Clean, ready-to-compile C# with no manual fixup |
| **Xcode compatibility** | Broken on modern Xcode without workarounds | Works with current Xcode and toolchains |
| **Maintenance** | Unmaintained | Actively developed, MIT licensed |
| **SwiftUI** | Not possible | Automatic UIHostingController bridge generation |
| **Mixed frameworks** | Objective-C only | Handles mixed Swift + Objective-C (e.g., Stripe) |
| **Project generation** | Partial — still requires manual setup | Complete .csproj + NuGet packaging |
| **Documentation** | Limited, outdated | Auto-generated XML doc comments from Swift source |
| **Time to first binding** | Hours to days of manual cleanup | Minutes — generate, build, pack |

### What About NativeLibraryInterop?

The [.NET MAUI Community Toolkit NativeLibraryInterop](https://github.com/CommunityToolkit/Maui.NativeLibraryInterop) is another approach developers turn to for consuming native libraries. It provides scaffolding to help you write Swift proxy code that bridges to C# through Objective-C interop. However, it also hasn't been updated in over a year.

While NativeLibraryInterop makes the proxy approach more structured, it still requires you to manually write and maintain Swift wrapper code for every API you want to expose. For large libraries with hundreds of types and methods, that's a significant amount of hand-written boilerplate — and it needs to be updated every time the native library changes.

swift-dotnet-bindings eliminates this entirely. There's no proxy code to write or maintain — the generator reads the compiled framework directly and produces the complete binding automatically.

## SwiftUI Interop for .NET MAUI

**Unique in the ecosystem**: swift-dotnet-bindings can automatically generate `UIHostingController` wrappers for SwiftUI views, letting you embed SwiftUI components directly in your .NET MAUI app. No manual bridging code, no Objective-C intermediaries.

This opens up Apple's modern UI framework from .NET — something that was previously impossible without writing significant Swift proxy code yourself. As Apple continues moving toward SwiftUI-first APIs, this capability becomes increasingly critical.

See the [SwiftUI Interop guide](https://github.com/justinwojo/swift-dotnet-bindings/wiki/SwiftUI-Interop) for detailed setup instructions.

## What Swift and Objective-C Features Are Supported?

The generator handles the full breadth of Swift's type system, not just the basics:

- **Classes** with automatic reference counting (ARC) and inheritance
- **Structs** — both frozen and non-frozen, with full value semantics
- **Enums** including associated values and raw types
- **Protocols** with interface generation and witness table dispatch
- **Generics** with proper C# generic constraints
- **Async methods** mapped to `Task<T>` with full cancellation support
- **Closures** as `Action<T>` / `Func<T>` with proper memory management
- **Optional types** mapped naturally to C# nullable types
- **SwiftUI Views** automatically bridged via `UIHostingController`
- **Properties**, **subscripts**, **operators**, and **static members**
- **Objective-C frameworks** with `[Protocol]`, `[Model]`, `[Export]`, argument semantics — everything Objective Sharpie does and more

### Type Mapping

Swift and Objective-C types are mapped to their natural C# equivalents. No `SwiftString` or `SwiftBool` leaking into your API:

| Swift Type | C# Type |
|---|---|
| `String` | `string` |
| `Bool` | `bool` |
| `Int`, `Int32`, `Int64` | `nint`, `int`, `long` |
| `Double`, `Float` | `double`, `float` |
| `Array<T>` | `IReadOnlyList<T>` |
| `Optional<T>` | `T?` |
| `Data` | `NSData` |
| `URL` | `NSUrl` |
| `CGRect`, `CGPoint`, `CGSize` | `CoreGraphics.CGRect`, etc. |

The generator also bridges to existing .NET iOS types where they exist, so `CGRect` from a Swift binding is the same `CoreGraphics.CGRect` you already use — no type mismatch headaches.

## Validated Against Real-World Libraries

This isn't a proof of concept. The tool is validated against **51 libraries across 95 framework targets**, including:

- **Image & Animation** — Nuke, Lottie, Kingfisher, SDWebImage
- **Networking** — Alamofire, Starscream, Moya
- **Payments** — Stripe (14 mixed Swift + ObjC frameworks)
- **Document Scanning** — BlinkID, BlinkIDUX
- **UI Components** — SnapKit, Hero, IQKeyboardManager, Charts
- **Database** — Realm (Objective-C)
- **Firebase** — 28 framework targets
- **Utilities** — CryptoSwift, SwiftyJSON, KeychainAccess, PromiseKit

This includes complex mixed Swift + Objective-C frameworks like Stripe (14 inter-dependent targets) and the full Firebase SDK (28 framework targets) — not just simple single-module libraries.

For select libraries, full end-to-end runtime testing is performed on both iOS Simulator and physical devices to verify the bindings actually work at runtime, not just compile.

## Pre-Built NuGet Packages

Don't want to generate bindings yourself? Pre-built NuGet packages are available for popular libraries:

- [`SwiftBindings.Nuke`](https://www.nuget.org/packages/SwiftBindings.Nuke) — High-performance image loading
- [`SwiftBindings.Lottie`](https://www.nuget.org/packages/SwiftBindings.Lottie) — Lottie animation playback

More are coming soon, including BlinkID, Stripe, and others. The [swift-dotnet-packages](https://github.com/justinwojo/swift-dotnet-packages) repository tracks all available packages and includes a sample .NET iOS app demonstrating real usage.

## Getting Started

### Prerequisites

- .NET 10.0 SDK (preview or later)
- macOS with Xcode installed
- An xcframework to bind (or use [spm-to-xcframework](https://github.com/justinwojo/spm-to-xcframework) to build one from any Swift Package Manager dependency)

### Step 1: Install the Template

```bash
dotnet new install SwiftBindings.Templates
```

### Step 2: Create a Binding Project

```bash
dotnet new swift-binding -n Nuke.Swift.iOS
```

### Step 3: Add Your Framework

Copy your `.xcframework` into the project directory (or configure the path in the `.csproj`).

### Step 4: Build and Pack

```bash
dotnet build
dotnet pack
```

The output is a standard NuGet package you can reference from any .NET MAUI or .NET iOS project — complete with generated XML documentation, proper framework references, and NuGet metadata.

**Ready to try it?** [Install the template](https://github.com/justinwojo/swift-dotnet-bindings/wiki/Getting-Started) and bind your first library in minutes, or [star the repo on GitHub](https://github.com/justinwojo/swift-dotnet-bindings) to follow along.

### Working with Swift Package Manager Dependencies

Many Swift libraries are distributed via SPM rather than as pre-built xcframeworks. Use the companion tool [spm-to-xcframework](https://github.com/justinwojo/spm-to-xcframework) to convert any SPM package:

```bash
# Clone and run
./spm-to-xcframework https://github.com/kean/Nuke.git --version 12.8.0
```

This produces the xcframework files ready to feed into the binding generator.

## How It Works

Under the hood, the generator:

1. **Parses** the Swift ABI JSON metadata from the compiled xcframework
2. **Resolves** types, protocols, generics, and cross-module dependencies
3. **Generates** C# P/Invoke bindings using `@_cdecl` Swift wrapper functions for ABI stability
4. **Emits** a complete `.csproj` with proper framework references, NuGet metadata, and build configuration
5. **Produces** a companion Swift wrapper that the MSBuild SDK compiles automatically

For Objective-C frameworks, it reads the module headers and generates traditional `[DllImport]`-based bindings with full `[Protocol]`, `[Model]`, and `[Export]` attribute support.

The full architecture is documented in the [Architecture guide](https://github.com/justinwojo/swift-dotnet-bindings/wiki/Architecture).

## Open Source and Community

swift-dotnet-bindings is [MIT licensed](https://github.com/justinwojo/swift-dotnet-bindings) and actively maintained. Originally forked from Microsoft's experimental `dotnet/runtimelab` swift-bindings branch, it has grown from a proof-of-concept into a production-grade tool with 800+ commits, 9,000+ unit tests, and 1,200+ runtime tests.

- [GitHub Repository](https://github.com/justinwojo/swift-dotnet-bindings)
- [Documentation Wiki](https://github.com/justinwojo/swift-dotnet-bindings/wiki)
- [Getting Started Guide](https://github.com/justinwojo/swift-dotnet-bindings/wiki/Getting-Started)
- [FAQ](https://github.com/justinwojo/swift-dotnet-bindings/wiki/FAQ)
- [Pre-built Packages](https://github.com/justinwojo/swift-dotnet-packages)

If you're tired of fighting with Objective Sharpie, writing Swift proxy libraries, or being locked out of Swift-only Apple frameworks, give it a try. Star the repo, [install the template](https://github.com/justinwojo/swift-dotnet-bindings/wiki/Getting-Started), and bind your first library in minutes.

For the story behind how this tool was built, check out my earlier post: [Building a Swift + ObjC .NET Binding Tool Entirely with AI in 6 Weeks](/blog/swift-dotnet-bindings).
