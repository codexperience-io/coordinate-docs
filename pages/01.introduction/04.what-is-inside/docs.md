---
title: What's inside
taxonomy:
    category: docs
---

## What you get with Coordinate Library

As a library, Coordinate offers your some base Classes and Protocols that you can apply as you see fit in your code. 

We will show you how, and theres also an Example App, so you can see how it all works together.

#### Coordinated+Coordinating Protocols

You probably wont have to deal with this pair of protocols directly, unless you are subclassing a Coordinator, but its important to understand the simple concepts behind them, because they are the backbone of all Coordinators in this library.

In a parent-child relationship, as in a Navigation Flow, think of Coordinating as a the parent (it has Coordinating habilities) and the child is the Coordinated (has propreties that allows it to be Coordinated by a parent Coordinating). So, a Coordinator must at least conform to the Coordinated protocol, and if you want this Coordinator to manage other Coordinators then it must conform to Coordinating, which already conforms to Coordinated.

Simply put:

Coordinated
- Has the basic lifecycle logic behind all Coordinators
- Has a parent that conforms to Coordinating
- Cannot have children Coordinators

Coordinating
- Conforms to Coordinated
- Can have children Coordinators

The following figure illustrates this:

[insert figure here] 


#### Coordinator Class
 
To start, every Coordinator Class has a rootViewController that is (or inherits from) a UIViewController. Think of it as a wrapper that manages that rootViewController data and other business logic. For instance, if you need data to show a list of items in a UIViewController, then the Coordinator should take care of getting this data and passing it to its rootViewController. If the UIViewController needs to fetch new data, then it should send a request to its Coordinator to get that data, when this request is done, the Coordinator is responsible of passing it to the UIViewController so it can update the views. 

This class conforms only to Coordinated protocol and therefore cannot manage children Coordinators. You would typically use this for simple UIViewControllers.

[insert figure here]

#### ContainerCoordinator Class

This class extends the base Coordinator class and conforms to the Coordinating protocol. This allows this class to act as a Container of many Coordinators and manage how and when they are presented.
It has a rootViewController that will serve as the basis to present the children Coordinators UIViewControllers. Think of it like a Switch Coordinator, you would typically use this as a AppCoordinator to switch between Splash Screen, Login Screen and Main Navigation. Take a look at the example app to see how it is used.

[insert figure here]

#### NavigationCoordinator Class

This class extends from ContainerCoordinator but is specificly adapted for UINavigationController. This is necessary because the UINavigationController has some specific logic to it, like how the children UIViewControllers are shown or presented and how to manage this. We need to be able to intercept these changes in the Navigation Flow in order to setup the other Coordinators but this also gives you the chance to do other things, like preparing data or actions tracking, that normally would sit inside a UIViewController, making it hard to maintain. That way your UiViewController is kept free of Navigation responsabilities.
This can be user directly as a child Coordinator inside a ContainerCoordinator or TabBarCoordinator.

[insert figure here]

#### TabBarCoordinator Class

This class extends from ContainerCoordinator and has extra logic to handle the UITabBarController. This is necessary because we need to intercept the navigation flow between screens to be able to intercept it and make sure the Coordinators hierarchy is valid and that, when appliable, the dependencies are injected, the childre Coordinators are started, etc. The same reasons from NavigationCoordinator apply here.
This is typically used as the main Coordinator on an App, just like the UITabBarController is. Check out the Example App to see how this is used.

[insert figure here]

#### HasDependencies Protocol

This protocol is pretty straight forward to use. Conforming to it will allow your Coordinator to be injected with Dependencies from its parent automatically. 
Your Coordinator must conform to it by having a `dependencies` property that implements AppDependenciesProtocol. 
Check the Example App to see how the AppDependencies are created and passed along.

#### HasRoutes Protocol

This is a protocol that stats that your Coordinator has Routing feature. For the moment its just nominal but in the future it will try to manage automatically some of the Routing tasks for your. While these solutions are explored, you can check the Example App and see how Routing is implemented at the moment. Its already pretty straight forward and should be simple to implement things like validation before switching routes and also deep linking.


