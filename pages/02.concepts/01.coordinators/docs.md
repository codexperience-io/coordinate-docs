---
title: Coordinators
taxonomy:
    category: docs
highlight: 
	lines: false
---

### Coordinator Pattern

The _Coordinator Patter_ is a design pattern proposed by Soroush Khanlou at NSSpain 2015. To understand it, we highly recommend investing the time to watch the presentation video [https://vimeo.com/144116310](https://vimeo.com/144116310).

In summary, this pattern allow us to move some of the concerns a `UIViewController` has to a `Coordinator` class. And you can see in practice with **Coordinate** the many benefits this practice has. 

### Coordinators

**Coordinate** offers a set of `Coordinators` classes and Protocols. We outline the `Coordinators` classes in this section.

##### Coordinator Class

_Inheritance: Coordinator_

Does **not** manage children `Coordinators`.

This is the base `Coordinator` class, for cases that you do not need manage any child `Coordinator`. 
Intended to be used as a wrapper to your `UIViewController` and manage its data fetching and other concerns, keeping your `UIViewController` as simple as possible.

##### ContainerCoordinator Class

_Inheritance: Coordinator -> ContainerCoordinator_

Manages children `Coordinators`.

The `ContainerCoordinator` is the most basic Coordinator that can manage other children Coordinators. The idea is that you can use it as a "switch" between any kind of Coordinator.


##### NavigationCoordinator Class

_Inheritance: Coordinator -> ContainerCoordinator -> NavigationCoordinator_

Manages children `Coordinators`.

The `NavigationCoordinator` is a specialized ContainerCoordinator, to be used with a `UINavigationController` as rootViewController and has specific logic to cope with the intricacies of said `UINavigationController`.
It has specialized presentation methods that mirror the ones from `UINavigationController`

##### TabBarCoordinator Class

_Inheritance: Coordinator -> ContainerCoordinator -> TabBarCoordinator_

Manages children `Coordinators`.

The `TabBarCoordinator` is a specialized ContainerCoordinator, to be used with a `UITabBarCoordinator` as rootViewController and has specific logic to cope with the intricacies of said `UITabBarController`.
It has specialized presentation methods that mirror the ones from `UITabBarController`


### Structure

If you imagine the structure of an app as a tree with _nodes_ ,_branches_ and _leafs_, the `Coordinators` with children (ContainerCoordinator, NavigationCoordinator, TabBarCoordinator) are the _nodes_, the presentation methods are the _branches_ and the `Coordinators` without children are the _leafs_ (Coordinator)

To illustrate that, here is a simplified diagram of the navigation structure from the [**Example App**](https://github.com/codexperience-io/coordinate-example-app)

![Coordinate Exampe App Navigation Diagram](/images/coordinate-navigation-diagram.svg)

Notice how the `Coordinators` "wrap" the UIViewControllers. This is so that the `Coordinators` can take care of the Navigation and just display the "wrapped" UIViewControllers. That means, you won't be calling presentation methods directly on your UIViewControllers, like _func show(UIViewController, sender: Any?)_ or _func pushViewController(UIViewController, animated: Bool)_, but instead you will use **Events** for that, like we show bellow in **Navigation**
