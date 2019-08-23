---
title: Principles
taxonomy:
    category: docs
---

This library is only great if used correctly and sticking to these principles will help you unleash its potential. As your app grows, you will realise how flexible and maintainable it is, despite it size.

1. A Coordinator always has a fixed `rootViewController`, avoid managing more than 1 UIViewController per Coordinator.
2. Do not share `rootViewController` between Coordinators
2. A Coordinator does not present itself, it also does not tell the parent how to present any other Coordinator.
3. A Coordinator should not know where it is in the Navigation Flow, the parent Coordinator is responsible for managing it.
4. Use Coordinators presentation methods to present other Coordinators (like root(), present(), etc), and never UIViewController to UIViewController directly
5. If Data is needed in a specific UIViewController, let its Coordinator get this data from the store instead of receiving if from the parent Coordinator
6. Applying principle 1, split the logic in smaller Coordinators instead of big and complex Coordinators


The reasoning behind these principles is that you should use a Coordinator for each UIViewController presented in the Navigation Flow. This allows you to encapsulate and simplify your UIViewControllers and makes understanding your code base an easy task.