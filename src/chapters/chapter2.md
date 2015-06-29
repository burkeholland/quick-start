## App building blocks

Before you start coding the Groceries app it's important to understand a NativeScript app's folder structure. It'll help you understand where to place new files, as well as a bit of what's going with NativeScript on under the hood.

Go ahead and open the Groceries app's files up in your text editor of choice and let's dig in.

### Directory structure

To keep things simple, let's start by looking at the outer structure of the Groceries app:

```
.
└── Groceries
    ├── app
    │   └── ...
    ├── package.json
    └── platforms
        ├── android
        └── ios
```

- **app**: The `app` folder contains all the development resources you need to build your app. Normally, you only touch code in the `app` folder during your development flow.
- **package.json**: This contains configuration about your app, such as you app id, the version of NativeScript you're using, and also which npm modules your app uses. We'll take a closer look at how to use this file in your app in chapter 5.
- **platforms**: This folders contains the platform-specific code NativeScript needs to build native iOS and Android apps. For instance in the `android` folder you'll find things like your project's `AndroidManifest.xml` and its .apk executable files. Similarly, in the `ios` folder, you'll find the Groceries' Xcode project and the .ipa file NativeScript builds when deploying to an emulator or device.

The NativeScript CLI manages the `platforms` for you as you develop and run your app; therefore it's a best practice to treat the `platforms` folder as generated code. The Groceries app includes `platforms/` in its `.gitignore` to exclude the files from source control.

Next, let's dig into the `app` folder, as that's where you'll be spending the majority of your time.

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
    │   │       ├── login.js
    │   │       └── login.xml
    │   ├── app.css
    │   ├── app.js
    │   └── ...
    └── ...
```

Let's look at what each of these files and folders do:

- **App_Resources**: Contains platform-specific resources such as icons, splash screens, and configuration files. The NativeScript CLI takes care of injecting these resources into the appropriate places in the `platforms` folder when you execute `tns run`.
- **shared**: This folder contains any files you need to share across views in your app. In the Groceries app you find a few model objects and a `config.js` file used to share configuration variables like API keys.
- **tns_modules**: This folder contains the NativeScript-provided modules you'll use to build your app. Each module contains platform-specific code (camera, http, file system, etc), exposed through a platform-agnostic API (e.g. `http.getJSON()`). We'll look at some examples momentarily.
- **views**: Each of your app's views will have a subfolder in `views`. Each view is made up of an XML file, a JavaScript file, and an optional CSS file. For now on the login view is in this folder, but you'll be adding a few more later in this guide.
- **app.css**: Contains global styles for your app. We'll dig into app styling in section 2.4.
- **app.js**: Sets up your application's starting module and initializes the app.

Take a look at `app/app.js`. This is the starting point for your app development, but it only contains three lines: 

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

- Notice the function that is invoked when the Page is loaded: we'll take a look at the 'load' function in the code-behind file that we'll construct next. 
- Notice also the way we load an image, with its source as "res://logo" and its stretch attribute set to 'none'. We'll talk more about handling images below as well. 
- Finally, note the TextField's two-way binding, set up to bind its text to "user.email_address" or "user.password". We'll also discuss data-binding below.


### Code-behind files

Although you can now see your login and registration screens, they are not yet wired up to send data to the backend. Let's fix that.

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

