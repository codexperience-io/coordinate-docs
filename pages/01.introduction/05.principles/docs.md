---
title: Principles
taxonomy:
    category: docs
---

This library is only great if used correctly and sticking to these principles will help you unleash its potential. As your app grows, you will realize how flexible and maintainable it is, despite it size.

1. A Coordinator always has a fixed `rootViewController`, that is **never** shared between `Coordinators`.
2. A Coordinator should not know where it is in the Navigation Flow, the parent Coordinator is responsible for presenting it.
3. Use Coordinators presentation methods to present other Coordinators (like root(), present(), etc), and never UIViewController to UIViewController directly
4. UIViewControllers get their data from the parent `Coordinator`, never subscribe directly to any data store or make network requests.  

The reasoning behind these principles is that you should use a Coordinator for each UIViewController presented in the Navigation Flow. This allows you to encapsulate and simplify your UIViewControllers and makes understanding your code base an easy task.