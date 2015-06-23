## Getting up and running

There are two ways to use NativeScript: through the NativeScript Command-Line Interface (CLI) and through [Telerik AppBuilder](http://www.telerik.com/appbuilder). Although the NativeScript framework itself is the same regardless of whether you use the CLI or AppBuilder, the way you interface with NativeScript—how you run your app, how you change configuration files, and so forth—differs based on the interface you choose.

For this guide, we're assuming that you are going to develop your app on your local computer using the NativeScript CLI. Although most of the steps and advice given in this guide apply equally to using NativeScript through the CLI or AppBuilder, some—mostly related to development workflow—are specific to using NativeScript through the NativeScript CLI.

### Install NativeScript and Configure your Environment

The NativeScript CLI has a few system requirements you must complete before building NativeScript apps. As a first step start by going through the appropriate instructions below depending on your development machine's operating system:

- [Windows](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-win.html)
- [OS X](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-os-x.html)
- [Linux](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-linux.html)

Once you have the setup complete, use the `npm install` command to install the NativeScript CLI itself:

```
$ npm install -g nativescript
```

You should now have two command available from your terminal: `tns`—which is short for **T**elerik **N**ative**S**cript—and `nativescript`. The two commands are equivalent, so we'll stick with the shorter `tns` command throughout this guide.

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

With NativeScript properly installed, you start working on a basic app. Navigate to a folder where you want to keep your app's code and clone the Groceries repo:

```
$ git clone https://github.com/tjvantoll/groceries.git
$ cd groceries
```

>Note, the full codebase is available on the master branch. Use the start branch for your base project, and feel free to refer to master if you get stuck.

You now have the starter code available for this app. Add both the iOS and Android platforms to this folder:

```
tns platform add ios
tns platform add android
```

If you'd like to see the app run in an emulator, you can run it now:

```
tns run ios --emulator
``` 
or 
```
tns run android --emulator
```

You'll find that running the app in an iOS emulator vs. an Android emulator offers a different user experience to the user. This is because NativeScript is levering native code to present the UI.

We recommend using [JSHint](http://jshint.com/) and [JSCS](http://jscs.info/) for code linting, a process to check for mistakes and to help you neaten your code. To use these helpers, install the app's dependencies via npm:

```
$ npm install
```

then use the app's gulp lint command:

```
$ gulp lint
```

Now that you have your repository ready, your environment configured, and your app ready to emulate for iOS and Android, you're ready to start taking a look at the code structure.

### A Good NativeScript Workflow

At this point, you have the NativeScript CLI downloaded and installed, as well as the iOS and Android dependencies that you need to run your app on both the iOS and Android emulators. Now you need a good development workflow to ease development. [This article](http://developer.telerik.com/featured/a-nativescript-development-workflow-for-sublime-text/) elaborates on a good workflow process that uses node, Sublime Text 3, and a Sublime Package to make building your NativeScript app for an emulator really fast and easy. We recommend that you configure your environment this way to save time.



