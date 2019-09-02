---
title: Short Intro
taxonomy:
    category: docs
---

The best way to explain how to use the `Coordinate` library, is to show you on a working example and go through the code, so that you can understand step by step how and why things are done.

This is a simple App for viewing a list of racing teams and their respective drivers. The full list of features is the following:
- A splash screen
- Main Navigation, made of a 3 tabs:
	- Teams
	- Drivers
	- Profile
- Teams and Drivers are lists of items
- Clicking on a Team or Driver item should show the detail view of the respective item
- Teams and Drivers can be favorited 
- Profile tab shows and manages the list of favorited items

Pretty simple, right? The idea is to illustrate how simple it is to **re-use** UIViewControllers, independent of the where they are in the Navigation Flow and also how easy it is to communicaty between the varios parts of the structure.

Download the [Coordinate Example App](https://github.com/codexperience-io/coordinate-example-app) at [https://github.com/codexperience-io/coordinate-example-app](https://github.com/codexperience-io/coordinate-example-app) and checkout the code yourself. 

In this chapter, the code from the App is used to explain how to use the library.