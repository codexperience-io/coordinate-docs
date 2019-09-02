---
title: Motivation
taxonomy:
    category: docs
---

#### Why

As we start programming for iOS we use the `UIViewControllers` as the pillar of our apps, they handle the UI, business logic, how the user navigates, among other things, but as we get more experienced and our apps more complex and bigger, we realize that it is not such a good idea to have the `UIViewControllers` handle so many things, and some problems start to arise: 
- The `UIViewControllers` get bigger and bigger, also know as **Massive View Controllers**.
- **Hard to understand the _Navigation Flow_** because the logic for that is buried inside different `UIViewControllers` in different methods, like _IBAction_ or _tableView(UITableView, didSelectRowAt: IndexPath)_ .
- As `UIViewControllers` do too many things your **code can easily get entangled** in itself and with others, as they have to know who are its parents and what comes next in the navigation flow.
- **Hard to test `UIViewControllers`** because you have to mock and simulate so much in order to test even a small part of the logic.

This leads to a variety of consequences, like **rigidity**, **immobility**, **repetition**, **complexity** and others that ultimately make your code harder to **maintain**, **change** or **refactor** and also **hard for others to understand**, even yourself sometimes can get lost. 

#### How

The solution starts by reducing the amount of things the `UIViewControllers` have to do. For that, **Coordinate** implements the _Coordinator Pattern_. By doing so, the `Coordinators` become a clear location for you to implement the **Navigation** and **Data Fetching** logic, leaving your `UIViewControllers` responsible for only **handling the UI**, **managing subviews**, **user input** and **model mutation** which means, your `UIViewControllers` become very much decoupled from the rest, making it **easier to test**, **easier to re-use** and so on.

The result is an scalable, flexibly and highly maintainable architecture where it is very easy to follow the navigation flow, understand what is going on and how, freeing you with time to implement features and business logic instead of constantly fixing and refactoring. 

#### Conclusion

Of course, there has been attempts to fix these same issues, but often the solutions, like frameworks, force you into a high level of abstraction that in itself can lead to many other problems, not to mention the steep learning curve. Our approach is to provide a simple library that can help you avoid these problems, but is simple to understand, without much abstraction or _magic_ and allows you to easily combine with other solutions you may want to use. In fact we try to keep it as simple as possible so that you also feel motivated to change it and contribute.  

The _Coordinator Pattern_ was first presented by Soroush Khanlou at NSSpain 2015 (check [https://vimeo.com/144116310](https://vimeo.com/144116310)). And for the implementation, **Coordinate** is inspired by some of the ideas of the implementation made by Aleksandar Vacić (check [https://github.com/radianttap/Coordinator](https://github.com/radianttap/Coordinator)), re-used what works well and introduced some different concepts.

