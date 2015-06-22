## Getting up and running

For this guide, we're assuming that you are going to develop your app on your local computer using the NativeScript Command Line Interface, or CLI. To start building your NativeScript app, you need to configure your local computer. To start, please visit the appropriate page according to your preferred development environment and follow the directions to install the proper software:

### Install NativeScript and Configure your Environment

Step 1:

- [Windows](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-win.html)
- [OS X](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-os-x.html)
- [Linux](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-linux.html)

Step 2: 

Install the CLI: [CLI Installation](http://docs.nativescript.org/setup/ab-setup/ab-cli-setup.html)

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



