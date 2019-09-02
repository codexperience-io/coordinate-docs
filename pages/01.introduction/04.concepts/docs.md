---
title: Concepts
taxonomy:
    category: docs
highlight: 
	lines: false
---

There are a few concepts that need to be understood before you dive into the code and try **Coordinate"** yourself. But we got you covered, we built an [**Example App**](https://github.com/codexperience-io/coordinate-example-app) using **Coordinate** and made it open source so you can follow along the explanation and see it applied in code. 

! Get the Example App at [https://github.com/codexperience-io/coordinate-example-app](https://github.com/codexperience-io/coordinate-example-app).

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


##### NavigationCoordinator

_Inheritance: Coordinator -> ContainerCoordinator -> NavigationCoordinator_

Manages children `Coordinators`.

The `NavigationCoordinator` is a specialized ContainerCoordinator, to be used with a `UINavigationController` as rootViewController and has specific logic to cope with the intricacies of said `UINavigationController`.
It has specialized presentation methods that mirror the ones from `UINavigationController`

##### TabBarCoordinator

_Inheritance: Coordinator -> ContainerCoordinator -> TabBarCoordinator_

Manages children `Coordinators`.

The `TabBarCoordinator` is a specialized ContainerCoordinator, to be used with a `UITabBarCoordinator` as rootViewController and has specific logic to cope with the intricacies of said `UITabBarController`.
It has specialized presentation methods that mirror the ones from `UITabBarController`

### Structure

If you imagine the structure of an app as a tree with _nodes_ ,_branches_ and _leafs_, the `Coordinators` with children (ContainerCoordinator, NavigationCoordinator, TabBarCoordinator) are the _nodes_, the presentation methods are the _branches_ and the `Coordinators` without children are the _leafs_ (Coordinator)

To illustrate that, here is a simplified diagram of the navigation structure from the [**Example App**](https://github.com/codexperience-io/coordinate-example-app)

![Coordinate Exampe App Navigation Diagram](/images/coordinate-navigation-diagram.svg)

Notice how the `Coordinators` "wrap" the UIViewControllers. This is so that the `Coordinators` can take care of the Navigation and just display the "wrapped" UIViewControllers. That means, you won't be calling presentation methods directly on your UIViewControllers, like _func show(UIViewController, sender: Any?)_ or _func pushViewController(UIViewController, animated: Bool)_, but instead you will use **Events** for that, like we show bellow in **Navigation**

### Events 

**Coordinate** implements an _Events mechanism_ whose _Events_ are nothing more than _enums_ defined in a _struct_. 

**_Events_** can be emitted by any subclass of `UIResponder` (like _UIViewController_ and _UIView_) and `Coordinators` and can be intercepted by their ancestor, whether it is the immediate _parentCoordinator_ or another `Coordinator` further above in the navigation flow.

This is the _Events propagation_ chart in a Coordinator - UIViewController chain

![Coordinate Events Diagram](/images/coordinate-events-diagram.svg) 

So how to use **Events**?

Here is an example of usage 

**Definition**

First you have to define it as an _enum_ inside a global _struct_ for scoping purposes, as in

```swift
struct AppEvents {
    ...

    enum Teams: CoordinateEvents, Equatable {
        ...
        case setFavorite(_ team: Team)
        ...
    }
    
}
```

Its a good practice to separate your _Events_ in domains and use a naming that is clear to understand

! The _enum_ must conform to **_CoordinateEvents_** and **_Equatable_** protocols

**Emission**

Then you have to **emit** it, in this case inside a callback to a _@IBAction_

```swift
@objc func favoriteButtonTapped(_ sender: UIButton) {
    guard let team = self.team else { return }
    
    emitEvent(AppEvents.Teams.setFavorite(team))
    ...
}
```

! The _rootViewController_ of a `Coordinator` must conform to **_Coordinated_** protocol. More on that bellow

and **Interception** 

Once emitted, an event will bubble up the Coordinator - UIViewController chain, where it can be intercepted at any point

Here it is intercepted inside a `Coordinator`


```swift
override func interceptEvent(_ event: CoordinateEvents) -> Bool {
    
    if let event = event as? AppEvents.Teams {
        if case .setFavorite(let team) = event {
            dependencies?.dataManager.setFavorite(team: team)
        }
    }
    ...
    return false
}
```

And then at another `Coordinator` higher on the hierarchy


```swift
override func interceptEvent(_ event: CoordinateEvents) -> Bool {
    
    if let event = event as? AppEvents.Teams {
        
        switch event {
        case .setFavorite:
            self.updateProfileTabIcon()
            return true
        ...
        }
        
    }
	...    
}
```

To intercept an event, just override **_func interceptEvent(\_ event: CoordinateEvents) -> Bool_** and react to the specific event you want. 

! If you return _true_ on this method, the event will **stop** bubbling up

With this mechanism in place, we can _send messages_ up on the hierarchy and _intercept_ them wherever we need it, without the need of using _Delegates_ or other mechanisms, that have their limitations outlined [here](http://aplus.rs/2017/highly-maintainable-app-architecture/) 

### Navigation

As you can see on the Example App structure diagram above, each `Coordinator` has its own UIViewController and only the `Coordinators` "talk" to each other, the UIViewControllers are isolated from each other.

So how do you tell the `Coordinator` where to navigate after the user taps a button (or a UITableViewCell)? You **emit** an _Event_. Like the following example

**Definition**

Similar to the way we did it above, we just add a new _Event_ to the Teams domain scope

```swift
struct AppEvents {
    ...

    enum Teams: CoordinateEvents, Equatable {
        ...
        case setFavorite(_ team: Team)
        case didSelect(_ team: Team)
        ...
    }
    
}
```

**Emission** 

In this case inside a _UITableViewController_ 

```swift
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    
    if data.indices.contains(indexPath.row) {
        let team = data[indexPath.row]
        
        emitEvent(AppEvents.Teams.didSelect(team))
    }
}
```

and **Interception** 

In this case, inside the `Coordinator` that manages the _UINavigationController_ so that it can tell that _UINavigationController_ that it should show the desired screen

```swift
override func interceptEvent(_ event: CoordinateEvents) -> Bool {
    if let event = event as? AppEvents.Teams {
    	if case .didSelect(let team) = event {
        	self.goTo(.team(team))
    	}
        return false
    }
    ...
}    
```

(explain why this is better than normal UIViewController or segues)

### Routing

As you saw in _Navigation_, we use _Events_ to send messages upwards in the hierarchy, but with _Routing_ we can define our navigation state in a top to bottom fashion, setting the _Route_ of the `Coordinators` downwards in the hierarchy.

For that, your `Coordinator` only needs to conform to the `HasRoutes` protocol, implement an _enum_ with the possible _Routes_ and the _goTo(\_ route: Route)_ method, like the following example

```swift 
extension TeamsCoordinator: HasRoutes {
    
    enum Route: Equatable {
        case list
        case team(_ team: Team)
    }
    
    func goTo(_ route: Route) {
        switch route {
        case .list:
            self.showListScreen()
        case .team(let team):
            self.showTeamScreen(team)
        
        self.activeRoute = route
    }
    
    private func showListScreen() {
        let coordinator = getCached(TeamsListCoordinator.self) ?? TeamsListCoordinator()
        self.root(coordinator)
    }
    
    private func showTeamScreen(_ team: Team) {
        let coordinator = getCached(TeamsDetailsCoordinator.self) ?? TeamsDetailsCoordinator()
        coordinator.team = team
        self.show(coordinator, sender: self)
    }
}
```

### Dependency Injection

In the source code from the [**Example App**](https://github.com/codexperience-io/coordinate-example-app) you can see how the _Data Manager_ inside the _App Dependencies_ _struct_ is used through the whole hierarchy. 

You define a global _struct_ that conforms to the `AppDependenciesProtocol` protocol and will hold all the necessary dependencies on your app

```swift
struct AppDependencies: AppDependenciesProtocol {
    
    let dataManager: DataManager
    
    init() {
        self.dataManager = DataManager()
    }
    
    func start(with completion: @escaping () -> Void = {}) {
        self.dataManager.start {
            completion()
        }
    }
}
```

On the first `Coordinator` in the hierarchy you need to initiate it, like in Example App AppCoordinator

```swift 
...
override func start(with completion: @escaping () -> Void = {}) {

    // Do not use the direct self.dependencies = dependencies way, because this method does additional things
    self.setDependencies(dependencies:  AppDependencies())
    
    self.dependencies?.start { [weak self] in
		...
    }
    
    super.start(with: completion)
}
...
```

and then from there on, you only need to conform to the `HasDependencies` protocol on the `Coordinators` that you want to have access to the _dependencies_. **Coordinate** takes care of injecting them automatically on the children `Coordinators` for your convenience.

```swift
class MainCoordinator: TabBarCoordinator<MainViewController>, HasDependencies {
    
    var dependencies: AppDependencies?
    ...
```

! Use the struct you created as a dependencies holder to conform to `HasDependencies` protocol, as shown in the example above. Do not use `AppDependenciesProtocol`.

The inspiration for this system comes from [here](http://aplus.rs/tags/coordinator/), where you can also read why it is a better solution for this problem. 