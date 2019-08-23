---
title: AppCoordinator
taxonomy:
    category: docs
---

### AppCoordinator

This is our main `Coordinator`, as seen on the _AppDelegate_ above. Here, the App's Navigation Flow is started to be built.

```swift
import UIKit
import CoreData
import Coordinate

class AppCoordinator: ContainerCoordinator<AppViewController>, HasDependencies {
    
    var dependencies: AppDependencies?
  
    var activeRoute: Route = .splash
    
    // MARK: - Coordinator lifecycle
    
    override func start(with completion: @escaping () -> Void = {}) {

        // Do not use the direct self.dependencies = dependencies way, because this method does additional things
        self.setDependencies(dependencies:  AppDependencies())
        
        self.dependencies?.start { [weak self] in
            // Set the default screen first
            self?.goTo(.splash)
        }
        
        super.start(with: completion)
    }
    
    // MARK: - App Life Cycle
    
    func applicationWillTerminate(_ application: UIApplication) {
        self.dependencies?.dataManager.save()
    }
    
    // MARK: - CoordinatingResponder
    
    override func splashDidFinish() {
        goTo(.mainNavigation)
    }
    
}

extension AppCoordinator: HasRoutes {
    // MARK: - Routing
    
    enum Route: Equatable {
        case splash
        case mainNavigation
    }
    
    func goTo(_ route: Route) {
        switch route {
        case .splash:
            self.showSplashScreen()
        case .mainNavigation:
            self.showMainNavigationScreen()
        }
        
        self.activeRoute = route
    }
    
    private func showSplashScreen() {
        let coordinator = getCached(SplashCoordinator.self) ?? SplashCoordinator()
        root(coordinator)
    }
    
    private func showMainNavigationScreen() {
        let coordinator = getCached(MainCoordinator.self) ?? MainCoordinator()
        root(coordinator)
    }
}
```

Code explanation:
- Line 5: Notice that **ContainerCoordinator** is used with **AppViewController**. This is very important, always pass here the _UIViewController_ type that you want it to be the _rootViewController_ of that _Coordinator_. The _Coordinator_ instantiates this class automatically and sets it as the _rootViewController_ property. In most of the cases this should be enough, instead of passing initialization arguments, you can override the _start_ method instead and set the properties from each Coordinator as you wish, but in any case you can always override this behaviour. Notice also that we conform to the _HasDependencies_ Protocol. This is a convenience protocol meant to simplify Dependency Injection through your Coordinators.
- Line 7: The _dependencies_ property will hold the Coordinator dependencies.
- Line 9: The _activeRoute_ is self explanatory too, it reflects the actual route in the Coordinator. More on that bellow.
- Line 13: Here we override the _start_ method and set dependencies.
- Line 20: When the dependencies are ready, we navigate to the Splash screen.
- Line 28: This is called from the _AppDelegate_ and we grab the DataManager from the dependencies and save its state when the App is about to terminate.
- Line 34: This is an override the _splashDidFinish_ method defined at _SplashCoordinating-Responder.swift_ file. This allows _AppCoordinator_ to react when its children call this method.
- Line 40: Making _AppCoordinator_ conform to _HasRoutes_ protocol by extension, so that its visually easier to find in the code where the _Routing_ logic is. This is just a style/organizational choice, you can conform to the protocol at the beginning of the file if you wish.
- Line 43: Defining _AppCoordinator_ routes
- Line 48: This is the core method to enable Routing, here we execute the logic for each _Route_. This is the place to use if you want to intercept navigation, for instance to check if the current user has permission to access it or not. If the route is changed, dont forget to set it to _activeRoute_ like we do here
- Line 60: We use the convenience method _getCached()_ from the `Coordinator`. This method returns the instance of the desired `Coordinator` if it was already instantiated. 
- Line 61: By calling _root()_ we tell the `Coordinator`to navigate to the selected `Coordinator`
