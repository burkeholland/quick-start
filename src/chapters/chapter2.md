## App building blocks

Now that you have your environment set up and the initial version of the app downloaded to your local computer, you can open it in your text editor and start exploring the codebase. Let's take a look at the directory structure of a NativeScript app.

### Directory structure

Here's the directory structure of this starter app:

```
.
└── Groceries
    ├── app
    │   ├── App_Resources
    │   │   ├── Android
    │   │   └── iOS
    │   ├── shared
    │   │   └── ...
    │   ├── tns_modules
    │   │   └── ...
    │   ├── views
    │   │   └── login
    │   │       ├── login-view-model.js
    │   │       ├── login.js
    │   │       └── login.xml
    │   ├── app.css
    │   ├── app.js
    │   └── ...
    └── platforms
        ├── android
        └── ios
```

Take a look at the two base parts of the app. 

- app: We have an app folder and a platforms folder. In the app folder we have all the resources we need for deploying our app. Normally, we only touch code in the app folder in terms of our development flow. 
- platforms: In the platforms folder, we have a lot of platform-specific code. When you ran 'tns platform add ios' and 'tns platform add android', these folders were created. If you dig into these folders, you'll find code that an android app needs to build such as AndroidManifest.xml, as well as the .apk files that are built when running the app. Similarly, in the ios folder, you'll find the Groceries xCode project and the .ipa file that is built when deploying to an emulator or device.

Consider the app directory as the development space for your app. In this folder are several subfolders:

- **App_Resources**: In this folder we separate iOS and Android images, such as the app icon
- **shared**: Models are in a shared folder so they can be accessed by all the app's modules. In the shared folder is also a config.js file where important items such as API keys are stored.
- **tns_modules**: Telerik NativeScript Modules are contained in this folder. They allow you to abstract platform-specific code into a platform-agnostic API, and the modules in this folder are ready-made for you to use. Read more about how tns modules work [here](http://developer.telerik.com/featured/nativescript-works/). 
- **views**: Each element of the app is contained in its own folder under 'view'.
- **app.css**: This file contains global styles for your app
- **app.js**: This file sets up your application's starting module and initializes the app

Take a look at app/app.js. This is the starting point for your app development, but it only contains three lines: 

```
var application = require("application");
application.mainModule = "./views/login/login";
application.start();
```

Here, we're requiring, or importing, the tns module 'application' which includes various other modules that are useful globally. Then, we set the main screen of our app to be the 'login' screen which we'll look at below. And then we start up the app which loads the css as you can discover by looking in for the loadCss() function in app/tns_modules/application/application-common.js.

Now that our app is ready for development, let's turn our attention to its UI.

### UI Components

First let's take a look at the UI components. You'll find several folders in app/views. Each folder contains one page of your app: list, login, and register are there already. If you look at app/views/login, you'll see three files. We're going to turn our attention to app/views/login/login.xml. 

In NativeScript's framework, you can construct a UI either using xml or JavaScript. Often, it's a bit easier to write your presentation layer in xml. Build out the login screen by adding some UI components, namely three boxes or "Borders", two textfields, and a button.

**Exercise: Add UI components to login.xml**

Under the Image where the logo currently resides, add the following code:

```
		<Border borderWidth="1" borderColor="#CECED2">
			<TextField id="email_address" hint="Email Address" />
		</Border>
		<Border borderWidth="1" borderColor="#CECED2">
			<TextField secure="true" hint="Password" />
		</Border>

		<Border borderWidth="1" borderColor="#0079FF">
			<Button text="Sign in" tap="signIn" />
		</Border>

		<Button text="Sign up for Groceries" tap="register" />
		
```
We've added four items to our screen, three of which are surrounded with a Border component to make it look like a box. 
- The TextField has the attributes you'd expect such as ids, hints, and the parameter 'secure' to ensure that a password isn't exposed. 
- The Button component has a tap function bound to it called 'signIn' which will enable the user to login. 
- Another button looks more like a link but also has a function bound to it to enable registration. 

If you run your app at this point, you'll see the form:

![login final](images/login-noregister.png)

Learn more about the UI components available in your app [here](http://docs.nativescript.org/ui-with-xml).

### Layouts 

You have many options in NativeScript when creating layouts. One of the simplest is demonstrated in login.xml, the StackLayout. Here, we see several UI components nested in StackLayout tags:

```
<StackLayout orientation="vertical" horizontalAlignment="center">
```

Each of those components will be stacked on top of each other, vertically. Learn more about creating NativeScript layouts [here](http://docs.nativescript.org/layouts) and [here](http://developer.telerik.com/featured/demystifying-nativescript-layouts/).

**Exercise: Create a stacked layout for our registration screen.**

In /views/register/register.xml, add the following markup:

```
<Page loaded="load">
	<StackLayout>
		<Image src="res://logo" stretch="none" horizontalAlignment="center"/>

		<Border borderWidth="1" borderColor="#CECED2">
			<TextField text="{{ user.email_address }}" id="email" hint="Email Address" keyboardType="email" />
		</Border>

		<Border borderWidth="1" borderColor="#CECED2">
			<TextField text="{{ user.password }}" secure="true" hint="Password" />
		</Border>

		<Border borderWidth="1" borderColor="#0079FF">
			<Button text="Sign Up" tap="register" />
		</Border>
	</StackLayout>
</Page>
```

If you run your code in an emulator, you'll find that you can now navigate to your registration view and back again by clicking the appropriate buttons.

- Notice the function that is invoked when the Page is loaded: we'll take a look at the 'load' function in the code-behind file that we'll construct next. 
- Notice also the way we load an image, with its source as "res://logo" and its stretch attribute set to 'none'. We'll talk more about handling images below as well. 
- Finally, note the TextField's two-way binding, set up to bind its text to "user.email_address" or "user.password". We'll also discuss data-binding below.


### Code-behind files

Although you can now see your registration screen, it's not yet wired up to send data to the backend. Let's fix that.

In app/views/login, you'll find login.js. This is called a 'code-behind' file because it supports the xml markup that constructs the presentation tier. 

**Exercise: Construct the login code-behind file.**

Add some required elements at the top of this file:

```
var view = require("ui/core/view");
var dialogs = require("ui/dialogs");
var frameModule = require("ui/frame");

var page;

var User = require("../../shared/models/User");
var user;
```

In login.xml, notice a 'load' function that, when the page is loaded, initializes several variables. Let's build up that load function in app/views/login/login.js:

```
exports.load = function(args) {

	user = new User();

	var page = args.object;
	page.bindingContext = user;
	
};
```
As we saw earlier, in login.xml, a button is bound to the signIn function:

```
<Button text="Sign in" tap="signIn" />
```

Construct the signIn function in app/views/login/login.js. :

```
exports.signIn = function() {
	user.login()
		.then(function() {
			frameModule.topmost().navigate("./views/list/list");
		}).catch(function() {
			dialogs.alert({
				message: "Unfortunately we could not find your account.",
				okButtonText: "OK"
			});
		});
};
```

NativeScript supports JavaScript modules and their implementation follows the [CommonJS specification](http://wiki.commonjs.org/wiki/CommonJS). Using the '[require](http://wiki.commonjs.org/wiki/Modules/1.1#Module_Context)' function at the top of this file allows us to identify a module to be imported, and it returns the exported API of this module.

Similarly, we see the variable 'exports', which is an object that the module may add its API to as it executes.

In this file, we export the functions 'load' and 'signIn'. These functions set up the login routine to pass data through to the Model. We'll visit the Model below.

