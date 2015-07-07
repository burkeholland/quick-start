## Application Logic

In this chapter, we'll learn about the base pattern on which the NativeScript framework is built, MVVM, or "Model, View, View Model". 

- Model: The model defines and represents the data. Separating the model from the various views that may use it allows for code reuse.
- View: The view represents the UI. The view is often data-bound to the View Model so that changes are instantly represented on the presentation tier.
- View Model: The View Model contains the application logic, exposing it for the View.

The most basic benefit of using this sort of separation between the Model, View, and View Model, is that we are able to craft two-way data binding such that the View Model can act as a switchboard between the Model and the View. Let's wire up our login xml to support binding.


### The View Model

Although you can now see your login screen as a nicely-styled entity with UI widgets and an image, it's not yet wired up to send data to the backend. Let's fix that.

In `app/views/login`, you'll find `login.js`. You're going to add all the functions that support the xml markup you created in the previous chapter into this file, which will become this page's View Model.

**Exercise: Construct the login View Model.**

First, add a 'loaded' attribute to the Page element at the top of `login.xml`:

```
<Page loaded="load">
```

Now you can create a load function in `app/views/login/login.js`:

```
exports.load = function() {
	console.log("hello")
};
```
If you run the app, you can see how, when the login screen loads, you can view the word 'hello' in the app console.

With this simple example, we can see how the xml file can append attributes to the markup that point to various functions in the accompanying JavaScript files. One such attribute is 'load', and another is 'tap'.

**Exercise: Add tap attributes to the login buttons and add their functions**

You can add a 'tap' attribute that will fire when a button is tapped or touched. 

In ```app/views/login/login.xml```, edit the markup for both buttons at the bottom of the screen:

```
<Button text="Sign in" tap="signIn" />

<Button text="Sign up for Groceries" tap="register" />
```
Then, in `apps/views/login/login.js`, create the 'signIn' and 'register' functions:
```
exports.signIn = function() {
	alert("Signing in");
};

exports.register = function() {
	alert("Registering");
};
```
At this point, if you run your app and tap either of the buttons in your simulator, you will see the appropriate alerts pop up. 

![login 5](images/login-stage5-ios.png)
![login 5](images/login-stage5-android.png)

Now that you can see tap gestures working, you can make them do something more interesting than open alerts.

### Navigating Screens

When you tap the "Sign Up for Groceries" button, you would expect a navigational change to a registration screen. This is very easy to do in NativeScript.

>While our Groceries app doesn't use complex navigation strategies, you have several available to you out of the box such as the [TabView](http://docs.nativescript.org/ui-views#tabview) and the [SegmentedBar](http://docs.nativescript.org/ui-views#segmentedbar)

**Exercise: Enable the "Sign Up" button on the login screen with a navigational change**

In `app/views/login/login.js`, add this line to the top of the file:

```
var frameModule = require("ui/frame");

```

Then, remove the alert in the register function and add the following function under the signIn function:

```
exports.register = function() {
	var topmost = frameModule.topmost();
	topmost.navigate("./views/register/register");
};

```
This function makes use of the module 'frameModule' which looks for the topmost frame and navigates to it. Here, we tell the topmost frame to navigate to the register view. If you run your code in an emulator, you'll find that you can now navigate to your registration view by clicking the "Sign Up for Groceries" button.

If you run your app and click this button, you will be sent to the registration screen, which we have pre-built for you. You can go ahead and create a new account for yourself if you like!

Learn more about how to link up your navigational strategies [here](http://docs.nativescript.org/navigation#navigation).

###Data Binding

Since we have been working with forms in NativeScript, we need to understand how data flows back and forth between the frontend and backend. 

**Exercise: Send data from the frontend to the View Model**

To get data to be sent from `login.xml`, add an id to the email textfield:

```
<TextField id="email_address" hint="Email Address" />
```

You need to access this textfield via the view, so add a line at the top, underneath the frameModule variable. Also expose email as a variable available to both the load and signIn functions:

```
var view = require("ui/core/view");
var email;
```
Finally, edit the load function in `app/views/login/login.js` to access the frontend via the UI element's id:

```
exports.load = function(args) {
	var page = args.object;
	email = view.getViewById(page, "email_address");	
};
```

Edit the signIn function to show the data input from the frontend:
```
exports.signIn = function() {
	console.log(email.text)
};
```

Now, if you run the app, you'll find data reaches the View Model when the signIn function is run via tapping the signIn button. In the next section, we'll show how to bind data back to the frontend.

###Connecting the View Model to a Model

Although you can now see data reaching the View Model from the frontend XML file, you need to do more than that to actually complete login. The data has to reach a backend service, which for this app is the Model and is found in `app/shared/Models/User.js`.

>A best practice for working within the MVVM pattern is to build the View Model first, before even touching the UI. The View Model shouldn't be aware of UI elements at all, it simply channels data back and forth.

**Exercise: Bind data back to the frontend**

You saw how to get data from the frontend to the backend, but what if you need to show some pre-filled values in the frontend?

To allow for two-way data binding, edit the textfield markup in `app/views/login/login.xml`:

```
<TextField id="email_address" text="{{ email_address }}" hint="Email Address" />
<TextField secure="true" text="{{ password }}" hint="Password" />
```

At the top of `app/views/login/login.js`, edit the top code block to include the following libraries:

```
var dialogs = require("ui/dialogs");
var frameModule = require("ui/frame");
var observable = require("data/observable");
var User = require("../../shared/models/User");
var view = require("ui/core/view");

var user = new observable.Observable();

```
We need dialogs to show a more complex popup than an alert can give us, and we need User to expose the User Model to the View Model.

 >**Observable** is a core building block for a View Model. It provides it with a mechanism required for two-way data binding so as to provide communication between the View and the View Model. This means that if the user updates the data in the UI the change will be reflected in the ViewModel and vice versa.

Now, edit the load function to hard-code some values that will show up on the frontend:

```
exports.load = function(args) {
	
	var page = args.object;
	
	var email = view.getViewById(page, "email_address");
	var password = view.getViewById(page, "password");

	user.set("email_address", "tj.vantoll@gmail.com");
	user.set("password", "password");

	page.bindingContext = user;
	
};
```

Now that you have the ability to bind the frontend and backends, you need to be able to send that data to a database in order to complete the login routine. If you run your app, you'll see the fields prefilled:

![login 5](images/login-stage5-ios.png)
![login 5](images/login-stage5-android.png)


### Connecting to a backend with the http module

You probably noticed, if you created your own user on the registration page, that, data was passing magically...somewhere. There's actually no magic involved; there is a config file that contains our API Key to [Telerik Backend Services](http://www.telerik.com/backend-services), a place where we are storing our users' information.

Take a look at `app/shared/config.js`. There's only a small code snippet there, but it includes a hard-coded API Key that we use throughout the app to access the backend (in real life, you would of course use your own API Key):

```
module.exports = {
	apiUrl: "http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/"
};
```

Note that The config file is used in all the Model files, for example in User Model: `app/shared/models/User.js`:

```
var config = require("../../shared/config");
```

**Exercise: Complete login**

To complete the login of a user, add a login function under the load function;

>*Todo*: hit the model properly in login.js

Now, if you rebuild and run your app in an emulator, you can login either using your credentials that you created earlier, or TJ's:
