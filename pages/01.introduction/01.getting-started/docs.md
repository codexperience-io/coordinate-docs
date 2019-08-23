---
title: Getting Started with Coordinate
taxonomy:
    category: docs
---

# The Coordinate Library

The Coordinate Library is a small library to help you get started with the Coordinator Pattern in your iOS App.
 
**Coordinate** provides a better way to structure your App's Navigation, using `Coordianotor` classes to build the Navigation Flow and deciding when and how your UIViewControllers are presented. This makes your UIViewControllers much more simple, easier to maintain, to test and trully encapsulated, as they do not know anything about their surroudings, like what other UIViewController presented them or what is the next UIViewController on the Navigation Flow. 

A second benefit is that by using `Coordinators` you can turn you UIViewControllers into simple input/output classes that do not need dependencies to get Data, the `Coordinator` talks with whatever data source it needs and passes to the UIViewControllers

The result is a much simpler architecture for your App, one that everybody can easily understand the Navigation Flow, one that you can easily re-use or move UIViewControllers without having to waste time refactoring and thus improving your productivity and maintanability of your App in the future.

## The Coordinator Pattern

The Coordinator Pattern is a design pattern for iOS, that allows you to create scalabe, flexibly and highly maintainable code. 

### Why? 

If you have been developing for iOS for a while, you probably have already experienced some of the following issues:  
- Massive View Controller, when a UIViewController get too big and hard to mantain, change and understand
- Entanglement between UIViewControllers, when the children know too much about the parent and how to present the next item in the navigation flow, this makes it very hard to change your code because if you change a Class, often you have to change 2 or more other just to change where it is in the navigation flow, without really changing any business logic
- Understanding the navigation flow of an App gets harder as it grows, because the code is spread around many UIViewControllers in different methods and often it is not clear what is the real sequence of navigation
- Testing complex UIViewControllers is hard, because often you have to mock stuff that you dont need just to test a small part of the logic

Why does this happen? UIViewControllers do too much, and this makes turns them into these "monster" UIViewControllers that we sometimes end up with. Normally a UIViewController has to take care of:
- Navigation Flow
- Data Fetching and Mutation
- Model-View binding
- User input
- Managing subviews

Of course there are frameworks out there that try to solve some of these issues but they often are too complex and/or introduce too much abstraction on top of Cocoa, making it hard to learn for new commers and other sort of problems that you must be familiar with if you tried them.

That is why using the Coordinatar Pattern is both easier and more maintainable, because to solve these problems you dont have to break ways with Cocoa, you will still need it.

### How? 

The way the Coordinator Pattern does this is by taking care of some of the responsabilities of the UIViewController:
- Navigation Flow
- Data Fetching and Mutation

By removing some of the concerns of the UIViewControllers and putting in on a place where it is easy to understand and maintain (a Coordinator), the UIViewControllers become simpler classes that receive an input and produce an output, without having to depend on anything else and without knowing where they are in the Navigation Flow, giving you the flexibility to re-use them or change where they appear, without changing a single line of code.

On top of that, theres an Example App that shows you how the concepts are used together, like Routing and Dependencies.

Now, the Coordinator Pattern in itself is not new, I belive it was first presented by Soroush Khanlou at NSSpain 2015 (check https://vimeo.com/144116310). And for the Coordinate Library initial implementation, I based myself on some of the ideas of the implementation made by Aleksandar Vacić (check https://github.com/radianttap/Coordinator), re-used some code and introduced some different concepts.
I did so because I realised that some issue arise when using the Coordinator Pattern in previous implementations. More on that in Prior Art and Motivation.
