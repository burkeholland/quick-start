## App building blocks

Now that you have your environment set up and the initial version of the app downloaded to your local computer, you can open it in the Sublime Text editor and start exploring the codebase. Let's take a look at the directory structure of a NativeScript app.

### Directory structure

Here's the directory structure of this starter app:


    └── app
        ├── App_Resources
        │   ├── Android
        │   └── iOS
        ├── shared
        │   ├── models
        │   │   ├── GroceryList.js
        │   │   └── Users.js
        │   └── config.js
        ├── tns_modules
        │   ├── LICENSE
        │   ├── application
        │   │   ├── application-common.js
        │   │   ├── application.android.js
        │   │   ├── application.ios.js
        │   │   └── package.json
        │   └── ...
        ├── assets
        ├── views
        │   ├── list
        │   │   ├── list-view-model.js
        │   │   ├── list.css
        │   │   ├── list.js
        │   │   └── list.xml
        │   ├── login
        │   │   ├── login-view-model.js
        │   │   ├── login.js
        │   │   └── login.xml
        │   ├── register
        │   │   ├── register-view-model.js
        │   │   ├── register.js
        │   │   └── register.xml
        ├── app.css
        ├── app.js
        ├── ...
        

- App_Resources: In this folder we separate iOS and Android images, such as the app icon
- shared: Models are in a shared folder so they can be accessed by all the app's modules. In the shared folder is also a config.js file where important items such as API keys are stored.
- tns_modules: Telerik NativeScript Modules are contained in this folder. They allow you to abstract platform-specific code into a platform-agnostic API, and the modules in this folder are ready-made for you to use. Read more about how tns modules work [here](http://developer.telerik.com/featured/nativescript-works/). 
- views: Each element of the app is contained in its own folder under 'view'.
- app.css: This file contains global styles for your app
- app.js: This file sets up your application's starting module and initializes the app


### UI Components

First let's take a look at the UI components. You'll find several folders in app/views. Each folder contains one page of your app: list, login, and register are there already. If you look at app/views/login, you'll see three files: 

login-view-model.js  
login.js  
login.xml  

In login.xml, you'll find the XML markup that creates your presentation tier. Notice the creation of Images, Borders, TextFields, and Buttons. These are all UI elements that can be used in a NativeScript app, constructed with XML. Learn more about the UI components available in your app [here](http://docs.nativescript.org/ui-with-xml).

### Navigation models

While our Groceries app doesn't use complex navigation strategies, you have several available to you to leverage. Out of the box, you can use:

[TabView](http://docs.nativescript.org/ui-views#tabview)  
[SegmentedBar](http://docs.nativescript.org/ui-views#segmentedbar)  

Learn more about how to link up your navigation UI [here](http://docs.nativescript.org/navigation#navigation).

### Layouts 

You have many options in NativeScript when creating layouts. One of the simplest is demonstrated in login.xml, the StackLayout. Here, we see several UI components nested in StackLayout tags:

```
<StackLayout orientation="vertical">
```

Each of those components will be stacked on top of each other, vertically. Learn more about creating NativeScript layouts here and here.

**Exercise: Based on the login code, let's create a frontend for our registration screen.**

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

Notice the function that is invoked when the Page is loaded: we'll take a look at the 'load' function in the code-behind file that we'll construct next. Notice also the way we load an image, with its source as "res://logo" and its stretch attribute set to 'none'. We'll talk more about handling images below as well. Finally, note the TextField's two-way binding, set up to bind its text to "user.email_address" or "user.password". We'll also discuss data-binding below.

### Code-behind files

In app/views/login, you'll find login.js. This is called a 'code-behind' file because it supports the xml markup that constructs the presentation tier. You'll find that in login.xml, a tap event is registered on the button at the bottom to login a user:

```
<Button text="Sign in" tap="signIn" />
```

You can find the signIn function in the code-behind file, where it simply invokes a View Model to perform a task:

```
exports.signIn = function() {
	viewModel.signIn();
};
```

**Exercise: Construct the registration code-behind file.**

In app/views/register/register.js, add the following code:


```
var view = require("ui/core/view");
var viewModel = require("./register-view-model");

exports.load = function(args) {
	var page = args.object;
	var email = view.getViewById(page, "email");

	page.bindingContext = viewModel;

	// Turn off autocorrect and autocapitalization for iOS
	if (email.ios) {
		email.ios.autocapitalizationType =
			UITextAutocapitalizationType.UITextAutocapitalizationTypeNone;
		email.ios.autocorrectionType =
			UITextAutocorrectionType.UITextAutocorrectionTypeNo;
		email.ios.keyboardType =
			UIKeyboardType.UIKeyboardTypeEmailAddress;
	}
};
exports.register = function() {
	viewModel.register();
};
```


Here we find the function 'load' to which we alluded earlier. In the code-behind file, we set up the screen to have certain behaviors on ios devices, in particular to turn off autocorrect and auto capitalization which is annoying when trying to register, as well as making the keyboard type be friendly for entering email addresses. 

This file also includes the register function which sets up the registration routine to pass through the View Model. Let's take a look at that View Model.

### View Model 

At the top of login.js, you see a variable created that references the View Model associated to this page:

```
var viewModel = require("./login-view-model");
```

As we saw above, that View Model is where the actual function to log a user in resides:

```
LoginViewModel.prototype.signIn = function() {
	this.get("user").login()
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

Some interesting things are happening here. First, in one line of code, we tell the user to login using the Model file that resides in app/shared/models. This file is called User.js. Then the View Model handles the behavior of the UI if the user passes or fails this test of his/her credentials.

To make use of the View Model and Model, we need to include the observable module by adding it at the top of app/shared/models/User.js:

```
var observableModule = require("data/observable");
```

The app uses the Prototype pattern to create a login routine and handle the result of the login action by making use of promises:

```
User.prototype = new observableModule.Observable();
User.prototype.login = function() {
	var that = this;
	return new Promise(function(resolve, reject) {
		http.request({
			url: config.apiUrl + "oauth/token",
			method: "POST",
			content: JSON.stringify({
				username: that.get("email_address"),
				password: that.get("password"),
				grant_type: "password"
			}),
			headers: {
				"Content-Type": "application/json"
			}
		}).then(function(data) {
			config.token = data.content.toJSON().Result.access_token;
			resolve();
		}).catch(function() {
			reject();
		});
	});
};
```

**Exercise: Construct the Registration View Model**

In app/views/register/register-view-model.js, add the following code:

```
var dialogs = require("ui/dialogs");
var frameModule = require("ui/frame");
var observable = require("data/observable");
var User = require("../../shared/models/User");

function RegisterViewModel() {
	this.set("user", new User());
}
RegisterViewModel.prototype = new observable.Observable();

RegisterViewModel.prototype.register = function() {
	this.get("user").register()
		.then(function() {
			dialogs
				.alert("Your account was successfully created.")
				.then(function() {
					frameModule.topmost().navigate("./views/login/login");
				});
		}).catch(function() {
			dialogs.alert({
				message: "Unfortunately we were unable to create your account.",
				okButtonText: "OK"
			});
		});
};

module.exports = new RegisterViewModel();
```

Note that, similarly to the way data is handled in login, we have set up a new prototype for registration, and invoked the function that is handled in app/shared/models/Users.js. The user is either registered or there is a problem, in which case a dialog pops up. We use the dialog ui by including it at the top of the View Model file, as you saw above.
 
We'll discuss the way the Model and View Model fit together in this framework in chapter 4. For now, observe the way the data flows: from the xml file to the code-behind .js file, to the View Model and then the Model, and back again to the frontend where we handle acceptance or rejection of these credentials.

### CSS

NativeScript supports a subset of CSS so that you can add styles to your app. We include global styles in app/app.css, where you'll find some styles for all the textfields, buttons and borders. You can also include individual css files in each view folder, which would be appropriate for styles that are isolated to a certain page. 

We don't have to worry about any particular CSS in the login or register screens, but let's turn our attention to the grocery list itself which appears after logging in. It needs a little massaging, so let's add a few CSS styles to app/views/list/list.css:

```
ListView {
	margin: 10;
}
Label {
	margin: 10;
}
Border {
	margin: 0;
}
```

This will give us a lttle more room for our groceries to display nicely.