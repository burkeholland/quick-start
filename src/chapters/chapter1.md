## Getting up and running

There are two ways to use NativeScript: through the NativeScript Command-Line Interface (CLI) and through [Telerik AppBuilder](http://www.telerik.com/appbuilder). Although the NativeScript framework itself is the same regardless of whether you use the CLI or AppBuilder, the way you interface with NativeScript—how you run your app, how you change configuration files, and so forth—differs based on the interface you choose.

For this guide, we're assuming that you are going to develop your app on your local computer using the NativeScript CLI. Although most of the steps and advice given in this guide apply equally to using NativeScript through the CLI or AppBuilder, some—mostly related to development workflow—are specific to using NativeScript through the NativeScript CLI.

### Install NativeScript and configure your environment

The NativeScript CLI has a few system requirements you must have in place before building NativeScript apps. As a first step start by going through the appropriate instructions below depending on your development machine's operating system:

- [Windows](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-win.html)
- [OS X](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-os-x.html)
- [Linux](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-linux.html)

Once you have the setup complete, use the `npm install` command to install the NativeScript CLI itself:

```
$ npm install -g nativescript
```

>**Tip:** If you have errors when running the `npm install -g nativescript` command, you may need to run the command with elevated permissions.
- **OS X / Linux:** Try running the command `sudo npm install -g nativescript`
- **Windows:** Make sure you open the command line as administrator.

You should now have two commands available from your terminal: `tns`—which is short for **T**elerik **N**ative**S**cript—and `nativescript`. The two commands are equivalent, so we'll stick with the shorter `tns` command throughout this guide.

You can verify the installation was successful by running `tns` in your terminal. You should see something like this:

```
$ tns
# NativeScript
┌─────────┬─────────────────────────────────────────────────────────────────────┐
│ Usage   │ Synopsis                                                            │
│ General │ $ tns <Command> [Command Parameters] [--command <Options>]          │
│ Alias   │ $ nativescript <Command> [Command Parameters] [--command <Options>] │
└─────────┴─────────────────────────────────────────────────────────────────────┘
```

### Start your app

With NativeScript CLI installed, it's time to start your app. Normally, a new NativeScript project is started by running `tns create`, which creates an empty NativeScript application e.g. `tns create hello-world` - but for this guide we've scaffolded out a boilerplate project to act as a starting point for [Groceries](https://github.com/NativeScript/sample-Groceries). To get it, navigate to a folder where you want to keep your app's code and clone the Groceries repo:

```
$ cd folder-you-want-groceries-to-be-in
$ git clone https://github.com/NativeScript/sample-Groceries.git
$ cd sample-Groceries
```

The master branch has the final state of the Groceries app. Feel free to refer back to it at any time, but for now switch over to the “start” branch for the guide's starting point:

```
$ git checkout start
```

### Add target development platforms

Your project is now setup, but before you run it, you need to tell NativeScript which mobile platform you want to build for using the `tns platform add` command. You can add multiple mobile platforms to a single project allowing you to deploy the same application to different mobile operating systems. Start by adding the iOS platform (if on a Mac):

```
$ tns platform add ios
```

The Android platform can be added in the same way

```
$ tns platform add android
```

>**Tip:** You can only add platforms for SDKs that you have already installed. See [Install NativeScript and configure your environment](#install-nativescript-and-configure-your-environment).

The `platform add` command adds a folder called `platforms` to your project. It copies all of the required native SDKs into this folder. When you eventually build the application later on, NativeScript will also copy your project code into the `platforms` folder so that a native binary can be created.

### Running your app

With the platform initialization complete you can now run your app in an emulator or on devices. If you're on an app start by running the app in an iOS simulator with the following command:

```
tns run ios --emulator
```

If all went well you should see something like this:

![login](images/login-intro1-ios.png)

Likewise, you can run your app in the Android emulator with the following command:

```
tns run android --emulator
```

If all went well you should see your app running in an Android emulator:

![login](images/login-intro1-android.png)

### Development workflow

At this point, you have the NativeScript CLI downloaded and installed, as well as the iOS and Android dependencies that you need to run your app on both the iOS and Android emulators. Now you need a good workflow that lets you make changes and see results fast.

> **TODO**: Discuss [LiveSync](https://github.com/NativeScript/nativescript-cli/issues/523), which is coming in version 1.2. Walk the reader through making a change and see it happen on iOS/Android. A gif would be good here.

>**A note about console.log**: As you run your app, you can watch the terminal window and see a lot of useful debugging code. Scroll up to find errors as they occur, and to view logging messages that you can include by adding `console.log("a test message")` anywhere where you want to check for bugs.

Now that you have your app created, your environment configured, and your app ready to emulate for iOS and Android, you're ready to start taking a look at the code structure.
