---
title: Dependency Injection
taxonomy:
    category: docs
highlight: 
    lines: false
---

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