## Application logic

In this chapter, you'll learn how to add JavaScript logic to your NativeScript app, and you'll be doing so using the base pattern on which the NativeScript framework is built, MVVM, or "model, view, view model". 

- Model: The model defines and represents the data. Separating the model from the various views that may use it allows for code reuse.
- View: The view represents the UI, written in XML. The view is often data-bound to the view model so that changes are instantly represented on the presentation tier.
- View Model: The view model contains the application logic (usually including the model) and exposes it to the view. NativeScript provides a module called 'Observable' that facilitates creating a view model object which can be bound to the view. We'll discuss that shortly.

The communication between the view and the model / view model is facilitated by the code-behind. This is the file that is named the same as the view, so in a given folder you will have file.xml and file.js representing the view and the code-behind, respectively.

The most basic benefit of using this sort of separation between the model, view, and view model, is that we are able to craft two-way data binding such that the view model can act as a switchboard between the model and the view. Let's wire up our login xml to support binding.

### The code-behind

Although you can now see your login screen as a nicely-styled entity with UI widgets and an image, it's not yet wired up to send data to the code-behind file. Let's fix that.

In `app/views/login`, you'll find `login.js`. This is the code-behind file for `login.xml` where you are going to add all the logic that support the xml markup you created in the previous chapter.

<h4 class="exercise-start">
    <b>Exercise</b>: Construct the login view model
</h4>

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
<div class="exercise-end"></div>

>The keyword 'exports' is standard in CommonJS, on which NativeScript's implementation of modules is based. In a module, a free variable called 'exports' is an object to which a module may add its API as it executes.In this case, using `exports` in a view model exposes the function for use in the view, or XML file. That is, using `exports.foo` in the view model is what makes `tap="foo"` in the view work. More information can be found on [the commonjs wiki](http://wiki.commonjs.org/wiki/Modules/1.1).

If you run the app, you can see how, when the login screen loads, you can view the word 'hello' in the app console.

With this simple example, you can see how you can append attributes to the xml of the view to run functions in the accompanying JavaScript files. One such attribute is 'load', and another is 'tap'.

<h4 class="exercise-start">
    <b>Exercise</b>: Add tap attributes to the login buttons and add their functions
</h4>

You can add a 'tap' attribute that will fire when a button is tapped or touched. 

In ```app/views/login/login.xml```, edit the markup for both buttons at the bottom of the screen:

```
<Button text="Sign in" tap="signIn" />

<Button text="Sign up for Groceries" tap="register" />
```
Then, at the bottom of the `app/views/login/login.js` file, create the 'signIn' and 'register' functions:
```
exports.signIn = function() {
	alert("Signing in");
};

exports.register = function() {
	alert("Registering");
};
```
<div class="exercise-end"></div>

At this point, if you run your app and tap either of the buttons in your simulator, you will see the appropriate alerts pop up. 

![login 5](images/login-stage5-ios.png)
![login 5](images/login-stage5-android.png)

Now that you can see tap gestures working, you can make them do something more interesting than open alerts.

### Navigating screens

When you tap the "Sign Up for Groceries" button, you would expect a navigational change to a registration screen. This is very easy to do in NativeScript.

<h4 class="exercise-start">
    <b>Exercise</b>: Enable the "Sign Up" button on the login screen with a navigational change
</h4>

In `app/views/login/login.js`, add this line to the top of the file:

```
var frameModule = require("ui/frame");

```

Then, replace the current register function with the version shown below:

```
exports.register = function() {
	var topmost = frameModule.topmost();
	topmost.navigate("./views/register/register");
};

```
<div class="exercise-end"></div>

This function makes use of the [frame module](http://docs.nativescript.org/ApiReference/ui/frame/README.html) which represents the view that is responsible for navigation in the app. Here, you tell the topmost frame, where the navigation occurs, to navigate to the register view. 

If you run your app and click this button, you will be sent to the registration screen, which we have pre-built for you. You can go ahead and create a new account for yourself if you like!

![navigate](images/navigate-ios.png)
![navigate](images/navigate-android.png)

>While our Groceries app doesn't use complex navigation strategies, you have several available to you out of the box such as the [TabView](http://docs.nativescript.org/ui-views#tabview) and the [SegmentedBar](http://docs.nativescript.org/ui-views#segmentedbar)

### Accessing UI components

Since you have been working with forms in NativeScript, you need to understand how data flows back and forth between the front end and back end. Specifically, you have created a login form, but you need to able to know what is typed into the fields and how to control the look and feel of the fields themselves. 

<h4 class="exercise-start">
    <b>Exercise</b>: Send data from the front end to the view model
</h4>

To get data to be sent from `login.xml`, add an id to the email textfield:

```
<TextField id="email_address" hint="Email Address" keyboardType="email" />
```

You need to access this TextField via the view, so in `app/views/login/login.js` add a line at the top, underneath the frameModule variable. Also expose email as a variable available to both the load and signIn functions:

```
var view = require("ui/core/view");
var email;
```
>Requiring the [view](https://docs.nativescript.org/ApiReference/ui/core/view/View.html) as a module gives you access to the view, which is the base class for all UI components, controlling the layout of a screen. By requiring a view, you can control the properties of both a view and its children.

Finally, edit the load function in `app/views/login/login.js` to get a reference to the email address text field:

```
exports.load = function(args) {
	var page = args.object;
	email = view.getViewById(page, "email_address");	
};
```
>Note the parameter passed into this function, 'args'. These are the children of the view that are laid out onto the screen when the screen is loaded.

Edit the signIn function to show the data input from the front end:
```
exports.signIn = function() {
	console.log(email.text)
};
```
<div class="exercise-end"></div>

Now, if you run the app, you'll find data reaches the view model when the signIn function is run via tapping the signIn button. 

By building JavaScript references to a given UI element, you can control how it looks and behaves on the front end. However, this is a very manual way to keep track of the state of the UI. This is where View Models come in.

### Add A View Model

NativeScript provides view model functionality in the form of a module called 'Observable'. 

Observable is the view model in the MVVM design pattern. It provides a mechanism used for two-way data binding so as to enable communication between the UI and code-behind. This means that if the user updates the data in the UI the change will be reflected in the model and vice versa. 

<h4 class="exercise-start">
    <b>Exercise</b>: Create a view model and bind it to the view
</h4>

To allow for two-way data binding using an Observable, replace the two existing TextField UI components with the two shown below, which include a new text attribute:

```
<TextField id="email_address" text="{{ email_address }}" hint="Email Address"  keyboardType="email" />
<TextField secure="true" text="{{ password }}" hint="Password" />
```

>The use of two curly brackets surrounding the text attribute delineates a data-bound value, which will be set by the view model.

At the top of `app/views/login/login.js`, edit the top code block to include the following libraries and a new User object, which you'll be using as this page's model:

```
var dialogs = require("ui/dialogs");
var frameModule = require("ui/frame");
var observableModule = require("data/observable");

var user = new observableModule.Observable({
    email_address: "user@domain.com",
    password: "password"
});

```

>You need to include the dialog module so that later on, we can show a more complex popup than an alert can give us. You'll use it later.
 
Now, edit the load function to specify the observable as the binding context for the page.

```
exports.load = function(args) {
	
	var page = args.object;
	
	page.bindingContext = user;
	
};
```

What's going on here?
- First, we're creating a user object that is based on the NativeScript observable module. The object is created with an "email_address" and "password" fields that are pre-populated.
- Then, we bind the page to the user view model.
- The values appear as bound values in the UI as delineated by the curly brackets you added above
  
<div class="exercise-end"></div>

If you run your app, you'll see the fields prefilled:

![login 5](images/login-stage5-ios.png)
![login 5](images/login-stage5-android.png)

Now that you have the ability to bind the UI to a view model, you need to be able to send that data to a database in order to complete the login routine. In the next chapter, you will connect the view and the view model to a back end service.

