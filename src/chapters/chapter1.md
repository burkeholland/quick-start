## Getting up and running

For this guide, we're assuming that you are going to develop your app on your local computer using the NativeScript Command Line Interace, or CLI. To start building your NativeScript app, you need to configure your local computer. To start, please visit the appropriate page according to your preferred development environment and follow the directions to install the proper software:

### Install NativeScript and Configure your Environment

**Step 1:**

- [Windows](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-win.html)
- [OS X](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-os-x.html)
- [Linux](http://docs.nativescript.org/setup/ns-cli-setup/ns-setup-linux.html)

Step 2: 

Install the CLI: [CLI Installation](http://docs.nativescript.org/setup/ab-setup/ab-cli-setup.html)

Step 3: 

Configure your IDE. The tools you use to develop locally are up to you, but a good workflow includes Sublime Text 2, as described [here](http://developer.telerik.com/featured/a-nativescript-development-workflow-for-sublime-text/).

### Start your app

With NativeScript properly installed, you start working on a basic app. Navigate to a folder where you want to keep your app's code and clone the Groceries repo:

`$ git clone https://github.com/tjvantoll/groceries.git`
`$ cd groceries`

Add both the iOS and Android platforms to this folder:

`tns platform add ios`
`tns platform add android`

If you'd like to see the app run in an emulator, you can run it now:

`tns run ios --emulator` or `tns run android --emulator`

Groceries uses [http://jshint.com/](JSHint) and [http://jscs.info/](JSCS) for code linting, a process to check for mistakes and to help you neaten your code. To use these helpers, install the app's dependencies via npm:

`$ npm install`

then use the app's gulp lint command:

`$ gulp lint`

Now that you have your repository ready, your environment configured, and your app ready to emulate for iOS and Android, you're ready to start taking a look at the code structure.



