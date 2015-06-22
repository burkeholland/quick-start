## Introduction

Welcome to the [NativeScript](https://nativescript.org) Getting Started Guide. Using this guide, you'll use NativeScript, a cross-platform JavaScript framework for building native mobile apps, to build an iOS and Android app from scratch.

![NativeScript.org logo](images/nativescript-logo.jpg)

### What you're building

Bored with creating yet another to-do app to learn about a new programming language or framework? So are we. That's why we added a small twist to revolutionize the to-do-list-app-as-a-means-to-learn-a-technology space—grocery management! With the rather unoriginally titled [“Groceries” app](https://github.com/tjvantoll/groceries), you'll no longer have to worry about forgetting the baking soda right when you have a craving for snickerdoodles. You'll be able to make your trip to Stop 'n' Shop that much easier.

Here's the full premise: your boss has tasked you with creating the next great grocery management app. It's totally going to make your company millions. You're told that the app must do the following things:

- Allow users to register and login.
- Allow authenticated users to add and delete groceries from a list.
- Connect to your companies' existing RESTful services.

Your boss also says the app has to look “native” on the iPhone 4s, 5, and 6, the iPad 2, and a variety of Android devices—including the CEO's old Nexus 7. The app also must be cross-platform, so that she can add groceries from her iPhone, and her significant other can add to the same list from their Nexus.

You're given some very native looking designs from your design team, a few endpoints from your backend team (below), and told to build an app out of it.

- GET http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/Groceries
    - Load all groceries
- POST http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/Groceries
    - Add a grocery to the list
- POST http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/Users
    - Register a new user
- POST http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/oauth/token
    - Generate a login token

Below are screenshots of what this app will look like after you complete this guide. Here's what it'll look like on iOS:

![login](images/login-screenshot.png)
![register](images/register-screenshot.png)
![list](images/list-screenshot.png)

And here's what the app will look like on Android:

![placeholder](images/screenshot-placeholder.png)
![placeholder](images/screenshot-placeholder.png)
![placeholder](images/screenshot-placeholder.png)

By building Groceries you'll see just how easy NativeScript makes building iOS and Android apps—and fun too! Let's get started.
