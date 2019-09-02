---
title: Events
taxonomy:
    category: docs
highlight: 
    lines: false
---

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
