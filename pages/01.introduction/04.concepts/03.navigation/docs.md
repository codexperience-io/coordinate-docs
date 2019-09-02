---
title: Navigation
taxonomy:
    category: docs
highlight: 
    lines: false
---

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

As you can start to realize, this is much better than using segues or telling the parent _navigationController_ how to present the next `UIViewController` because you do not have to make your `UIViewControllers` aware of the navigation flow, they do not need to know that they are a child of a `UINavigationController` or that what next screen in the navigation flow, in fact, they do not know anything about their surroundings, making them trully encapsulated and re-usable.