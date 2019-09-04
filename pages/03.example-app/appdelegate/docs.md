---
title: AppDelegate
taxonomy:
    category: docs
---

### AppDelegate

We start at the AppDelegate, it should have the following basic structure:

```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    private(set) var rootCoordinator: AppCoordinator = {
        return AppCoordinator()
    }()
    
    func application(_ application: UIApplication, willFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {
        window = UIWindow(frame: UIScreen.main.bounds)
        
        window?.rootViewController = rootCoordinator.rootViewController
        
        return true
    }

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        window?.makeKeyAndVisible()
        rootCoordinator.start()
        
        return true
    }

    ...

    func applicationWillTerminate(_ application: UIApplication) {
        // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
        // Saves changes in the application's managed object context before the application terminates.
        rootCoordinator.applicationWillTerminate(application)
    }
}
```

So lets go through the code: 

- Line 8: We define _rootCoordinator_ to hold the instance of the _AppCoordinator_, a subclass of ContainerCoordinator in this case. More on that bellow.
- Line 15: The _rootCoordinator.rootViewController_ is set as the _window.rootViewController_ because this the _UIViewController_ managed by the _AppCoordinator_ and there we will be able to build the rest of the structure
- Line 23: Here we tell the _rootCoordinator_ to start, when the App is ready.
- Line 33: To keep the AppDelegate clean and simple, we implement in the _rootCoordinator_ this method where we have a better access to all the navigation structure  