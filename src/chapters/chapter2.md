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

> Jen: I simplified the structure above considerably because I feel like what you had was too overwhelming. I also added a platforms folder, because it was missing from yours. I think you'll want to start by discussing the difference between the `app` and `platforms` folders, and then go on to discuss what's in `app`. You can refer to [the current quick start guide](http://docs.nativescript.org/hello-world/hello-world-ns-cli.html#4-explore-the-newly-created-project) for an example.

- **App_Resources**: In this folder we separate iOS and Android images, such as the app icon
- **shared**: Models are in a shared folder so they can be accessed by all the app's modules. In the shared folder is also a config.js file where important items such as API keys are stored.
- **tns_modules**: Telerik NativeScript Modules are contained in this folder. They allow you to abstract platform-specific code into a platform-agnostic API, and the modules in this folder are ready-made for you to use. Read more about how tns modules work [here](http://developer.telerik.com/featured/nativescript-works/). 
- **views**: Each element of the app is contained in its own folder under 'view'.
- **app.css**: This file contains global styles for your app
- **app.js**: This file sets up your application's starting module and initializes the app

> Jen: Have people open up `app.js` at this point so you can show people how control transfers from `app.js` to `login.xml`. It'll act as a segue into the next section.

### UI Components

First let's take a look at the UI components. You'll find several folders in app/views. Each folder contains one page of your app: list, login, and register are there already. If you look at app/views/login, you'll see three files: 

> Jen: I think we should start with the view model file not there, and just put everything in the code-behind file. As is someone will wonder why there is a file called “view-model”, and we don't want to give the explanation yet.

login-view-model.js  
login.js  
login.xml  

> Jen: There needs to be an exercise here. Personally I'd have the `login.xml` file start with some very simple content in the start branch (like, just the logo), and you could have people paste in the form here.

In login.xml, you'll find the XML markup that creates your presentation tier. Notice the creation of Images, Borders, TextFields, and Buttons. These are all UI elements that can be used in a NativeScript app, constructed with XML. Learn more about the UI components available in your app [here](http://docs.nativescript.org/ui-with-xml).

### Navigation models

> Jen: I think a discussion of code-behind files needs to come before navigation. As is, someone is going to open up `login.js` and have no idea what's going on. Personally I would start `login.js` completely empty and use it as an opportunity to explain CommonJS (`require()` and `export`). Have an exercise that adds a `loaded` function, then one that ties the `<Button>`'s `tap` event to a function, THEN discuss navigation.

While our Groceries app doesn't use complex navigation strategies, you have several available to you to leverage. Out of the box, you can use:

[TabView](http://docs.nativescript.org/ui-views#tabview)  
[SegmentedBar](http://docs.nativescript.org/ui-views#segmentedbar)

**Exercise: Enable the "Sign Up" button on the login screen with a navigational change**

Right now, if you were to click the Sign Up For Groceries button on the login screen, nothing would happen. Let's get this button to change the screen to show a registration form. 

In app/views/login/login-view-model.js, add the following function under the signIn function:

```
LoginViewModel.prototype.register = function() {
	var topmost = frameModule.topmost();
	topmost.navigate("./views/register/register");
};

```
This function makes use of the module 'frameModule' which looks for the topmost frame and navigates to it. Here, we tell the topmost frame to navigate to the register view. 

Learn more about how to link up your navigational strategies [here](http://docs.nativescript.org/navigation#navigation).

### Layouts 

You have many options in NativeScript when creating layouts. One of the simplest is demonstrated in login.xml, the StackLayout. Here, we see several UI components nested in StackLayout tags:

```
<StackLayout orientation="vertical">
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
};

exports.register = function() {
	viewModel.register();
};
```

Here we find the function 'load' to which we alluded earlier. This file also includes the register function which sets up the registration routine to pass through the View Model. Let's take a look at that View Model.

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
			//frameModule.topmost().navigate("./views/list/list");
		}).catch(function() {
			dialogs.alert({
				message: "Unfortunately we could not find your account.",
				okButtonText: "OK"
			});
		});
};
```

Some interesting things are happening here. First, in one line of code, we tell the user to login using the Model file that resides in app/shared/models. This file is called User.js. Then the View Model handles the behavior of the UI if the user passes or fails this test of his/her credentials.

To make use of the View Model and Model, note that we include the observable module by adding it at the top of app/shared/models/User.js:

```
var observableModule = require("data/observable");
```

The app uses the Prototype pattern to create a login routine and handle the result of the login action by making use of promises.


**Exercise: Construct the Registration View Model**

Let's get the View Model into place so that data from the frontend XML can filter through the code-behind file, through the View Model, and over to the Model. In app/views/register/register-view-model.js, add the following code:

```
var dialogs = require("ui/dialogs");
var frameModule = require("ui/frame");
var observable = require("data/observable");
var User = require("../../shared/models/User");
```

Here, we're including several NativeScript modules, including dialog, frameModule, and observable. We also include the User Model so that it is available for data pass-through.

Under that code, create a function that creates a new user based on the User Model:

```
function RegisterViewModel() {
	this.set("user", new User());
}
```

Then, we can use the prototype pattern to create a new bindable object, the RegisterViewModel:

```
RegisterViewModel.prototype = new observable.Observable();
```

Next, add in the register function to handle the way the UI will be have based on the response of the model to data passed to it:

```
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
```

And finally, export this new View Model which we have called RegisterViewModel:

```
module.exports = new RegisterViewModel();
```

Note that, similarly to the way data is handled in login, we have set up a new prototype for registration, and invoked the function that is handled in app/shared/models/Users.js. The user is either registered or there is a problem, in which case a dialog pops up. We use the dialog ui by including it at the top of the View Model file, as you saw above.

**Exercise: Wire up the registration function in the Model**

Let's get something to happen when we click the register button in the register screen. Add the following code to /app/shared/models/User.js:

```
User.prototype.register = function() {
	var that = this;
	return new Promise(function(resolve, reject) {
		http.request({
			url: config.apiUrl + "Users",
			method: "POST",
			content: JSON.stringify({
				Username: that.get("email_address"),
				Email: that.get("email_address"),
				Password: that.get("password")
			}),
			headers: {
				"Content-Type": "application/json"
			}
		}).then(function() {
			resolve();
		}).catch(function() {
			reject();
		});
	});
};
```

Now, if you rebuild and run your app in an emulator, you can register a new user! 

<img src="images/registration-success.png"/>
 
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