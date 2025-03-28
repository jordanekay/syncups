# SyncUps

This project demonstrates how to build a complex, real world application that deals with many forms
of navigation (_e.g._, sheets, drill-downs, alerts), many side effects (timers, speech recognizer,
data persistence), and do so in a way that is testable and modular.

This application was built over the course of [many episodes][modern-swiftui-collection] on
Point-Free, a video series exploring advanced programming topics in the Swift language, hosted by
[Brandon Williams](https://twitter.com/mbrandonw) and [Stephen
Celis](https://twitter.com/stephencelis).

<a href="https://www.pointfree.co/collections/swiftui/modern-swiftui">
  <img alt="video poster image" src="https://d3rccdn33rt8ze.cloudfront.net/episodes/0209.jpeg" width="600">
</a>

## Call to action!

While we have built the SyncUps application in the style that makes the most sense to us, we know 
that some of our choices aren’t for everyone. We would love if others fork this repo and rebuild 
it in the style of their choice.

Don’t like to use an observable model for each screen? Prefer to use `@EnvironmentObject` instead?
Want to use an architectural pattern such as VIPER? Have a different way of handling dependencies? 

Please show us! Just complete the following steps:

1. [Fork](https://github.com/pointfreeco/syncups/fork) our repo and rebuild the app any way you 
see fit. If you want to start with Apple’s code rather than ours, you can [download it 
here][scrumdinger-dl].
2. Update the Readme.md to describe your choices in rebuilding the app (see [below](#modern-swiftui)
for ours).
3. Open a PR to this repo where you add a link to your port in this readme.

We will collect links to the other ports so that there can be a single place to reference many 
different approaches for building the same application.

## Overview

The inspiration for this application comes Apple's [Scrumdinger][scrumdinger] tutorial:

> This module guides you through the development of Scrumdinger, an iOS app that helps users manage
> their daily scrums. To help keep scrums short and focused, Scrumdinger uses visual and audio cues
> to indicate when and how long each attendee should speak. The app also displays a progress screen
> that shows the time remaining in the meeting and creates a transcript that users can refer to
> later.

The Scrumdinger app is one of Apple's most interesting code samples as it deals with many real world
world problems that one faces in application development. It shows off many types of navigation,
it deals with complex effects such as timers and speech recognition, and it persists application
data to disk.

However, it is not necessarily built in the most ideal way. It uses mostly fire-and-forget style
navigation, which means you can't easily deep link into any screen of the app, which is handy for
push notifications and opening URLs. It also uses uncontrolled dependencies, including file system
access, timers and a speech recognizer, which makes it nearly impossible to write automated tests
and even hinders the ability to preview the app in Xcode previews.

But, the simplicity of Apple's Scrumdinger codebase is not a defect. In fact, it's a feature!
Apple's sample code is viewed by hundreds of thousands of developers across the world, and so its
goal is to be as approachable as possible in order to teach the basics of SwiftUI. But, that doesn't
mean there isn't room for improvement.

## Modern SwiftUI

Our SyncUps application is a rebuild of Apple's Scrumdinger application, but with a focus on
modern, best practices for SwiftUI development. We faithfully recreate the Scrumdinger, but with
some key additions:

 1. _All_ navigation is driven off of state, including sheets, drill-downs and alerts. This makes
    it possible to deep link into any screen of the app by just constructing a piece of state and
    handing it off to SwiftUI.
 1. Further, when a feature can navigate to multiple destinations, an enum is used to model the 
    destinations, which gives us compile time proof that two destinations cannot be active at the 
    same time. This cannot be accomplished with default SwiftUI tools, but can be done with our 
    [SwiftNavigation library][swift-nav-gh].
 1. Persistence is handled by our [Sharing][sharing-gh] library, which allows one to hold onto 
    shared state in an observable model or view, and under the hood any changes to the state will be
    persisted to external storage, such as the file system. Even the global navigation is persisted
    using the Sharing library.
 1. All side effects are controlled. This includes access to the file system for persistence, access
    to time-based asynchrony for timers, access to speech recognition APIs, and even the creation
    of dates and UUIDs. This allows us to run our application in specific execution contexts, which
    is very useful in tests and Xcode previews. We accomplish this using our
    [Dependencies][dependencies-gh] library.
 1. Identifiers are made type safe using our [Tagged library][tagged-gh]. This prevents us from
    writing non-sensical code, such as comparing a `SyncUp.ID` to a `Attendee.ID`.
 1. Instead of using bare arrays in feature logic we use an "identified" array from our
    [IdentifiedCollections][identified-collections-gh] library. This allows you to read and modify
    elements of the collection via their ID rather than positional index, which can be error prone
    and lead to bugs or crashes.
 1. The project includes a full test suite. Since all of navigation is driven off of state, and
    because we controlled all dependencies, we can write very comprehensive and nuanced tests. For
    example, we can write a unit test that proves that when a sync-up meeting's timer runs out the
    screen pops off the stack and a new transcript is added to the sync-up. Such a test would be
    very difficult, if not impossible, without controlling dependencies.

## Ports

Here is a list of ports of the app:

* [Scrumdinger](https://developer.apple.com/tutorials/app-dev-training/getting-started-with-scrumdinger): Apple's original Scrumdinger application.
* [Composable Architecture](https://github.com/pointfreeco/swift-composable-architecture/tree/main/Examples/SyncUps): A port of the SyncUps app to the Composable Architecture.
* [SharingGRDB](https://github.com/pointfreeco/sharing-grdb/tree/main/Examples/SyncUps): A port of SyncUps backed by SQLite.
* **Yours could be here! [Submit today](#Call-to-action)!**

[modern-swiftui-collection]: https://www.pointfree.co/collections/swiftui/modern-swiftui
[scrumdinger]: https://developer.apple.com/tutorials/app-dev-training/getting-started-with-scrumdinger
[scrumdinger-dl]: https://docs-assets.developer.apple.com/published/1ea2eec121b90031e354288912a76357/TranscribingSpeechToText.zip
[tagged-gh]: http://github.com/pointfreeco/swift-tagged
[identified-collections-gh]: http://github.com/pointfreeco/swift-identified-collections 
[swift-nav-gh]: http://github.com/pointfreeco/swift-navigation
[dependencies-gh]: http://github.com/pointfreeco/swift-dependencies 
[sharing-gh]: https://github.com/pointfreeco/swift-sharing
