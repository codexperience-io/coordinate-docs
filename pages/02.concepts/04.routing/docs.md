---
title: Routing
taxonomy:
    category: docs
highlight: 
    lines: false
---

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