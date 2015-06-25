## MVVM

### What is MVVM?

In this chapter, we'll learn about the base pattern on which the NativeScript framework is built, MVVM, or "Model, View, View Model". We've already seen several examples of the elements that are used in this design pattern:

- Model (like Users.js which handles the API call to the backend to login and register students). The model represents the data. Separating the model from the various views that may use it allows for code reuse.
- View (like the register.xml file we built). The view is often data-bound to the View Model so that changes are instantly represented on the presentation tier.
- View Model (like the register-view-model.js file that takes data from the frontend and passes it to the Model for processing). The View Model exposes the models and logic needed by the view, and handles presentation logic.

You can create your MVVM code structure like we did for login, with only login.xml and login.js communicating with User.js as its model.

###Data Binding

**Exercise: Bind data to the frontend of login.xml**

Instead of just showing a blank text field, we can help the user login more quickly by binding data to the login text fields. In app/views/login/login.xml, edit the username text field to show a default username:

```
<TextField text="{{ email_address }}" id="email_address" hint="Email Address" />
```

Similarly, edit the password field in this file:

```
<TextField secure="true" text="{{ password }}" hint="Password" />
```

This way, you can pull in values from the code-behind file into your app; add the following two lines at the bottom of the load function in app/views/login/login.js:

```
user.set("email_address", "tj.vantoll@gmail.com");
user.set("password", "password");
``` 

###Constructing a User View Model

While you can certainly keep your View Model functionality in the code-behind file as we did for the login routines, an alternate pattern involves separating the code-behind file from a view-model.js file. This separation helps you isolate concerns for more effective unit testing. Let's build out the registration form using this pattern.


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

### Connecting a Model and a View Model

Now that we have both login and registration routines complete, we need to work on the app's actual functionality as a grocery list management tool.

To be able to manage data in the grocery list, we need to build a connection between the presentation tier and the database as we did for registration. Let's build out these pieces in our grocery list.

**Exercise: Construct the list Model and View Model**

In app/views/list/list-view-model.js, add:

```
var dialogs = require("ui/dialogs");
var observable = require("data/observable");
var GroceryList = require("../../shared/models/GroceryList");

function ListViewModel() {
	this.set("grocery", "");
	this.set("groceryList", new GroceryList([]));
}
ListViewModel.prototype = new observable.Observable();

ListViewModel.prototype.reset = function() {
	this.get("groceryList").empty();
	this.get("groceryList").load();
};

module.exports = new ListViewModel();
```

Here, we're creating a new empty grocery array and a groceryList variable to instantiate our GroceryList object that we create in the Model. We're also emptying and refilling our groceryList in the reset function. Let's create the GroceryList object in the Model:

In app/shared/models/GroceryList.js, let's grab any Grocery data that might exist in the backend and push it into the grocery array:

```
var config = require("../../shared/config");
var http = require("http");
var observableArray = require("data/observable-array");

function GroceryList() {}
GroceryList.prototype = new observableArray.ObservableArray([]);

GroceryList.prototype.load = function() {
	var that = this;
	http.getJSON({
		url: config.apiUrl + "Groceries",
		method: "GET",
		headers: {
			"Authorization": "Bearer " + config.token
		}
	}).then(function(data) {
		data.Result.forEach(function(grocery) {
			that.push({ name: grocery.Name });
		});
	});
};

GroceryList.prototype.empty = function() {
	while (this.length) {
		this.pop();
	}
};

module.exports = GroceryList;
```
In the load function above, we are pulling down the list associated to the user's credentials. If you rebuild, run the app, and login as tj.vantoll@gmail.com, you'll find a list of groceries pulled from Backend services in the Groceries data type. It will look something like this:

<img src="images/list-view-1.png"/>

It's great that we can see data already in the database, but we also need to add some items. Let's build that functionality.

**Exercise: Add the ability to create a Grocery item**

Notice at the top of the list is a text area and an 'Add' button. Right now, it doesn't do anything. Let's fix that.

In app/views/list/list.js, add a function to respond to the 'add' tap event that is already in list.xml. We'll put this underneath the navigatedTo function.

```
exports.add = function() {
	view.getViewById(page, "grocery").dismissSoftInput();
	viewModel.add();
};
```

Notice that this function performs two tasks: first, it dismisses the keyboard, and then it calls the add function in the ViewModel. 

In app/views/list/list-view-model.js, let's add that 'add' function to pass data to the model:

```
ListViewModel.prototype.add = function() {
	this.get("groceryList").add(this.get("grocery"))
		.catch(function() {
			dialogs.alert({
				message: "An error occurred adding to your list.",
				okButtonText: "OK"
			});
		});
	this.set("grocery", "");
};
```
In this function, we get the 'grocery' item from the input field and add it to the groceryList object. 

Finally, let the manipulation of this new data be handled in the Model by adding the following function under the 'empty' function in /app/shared/models/GroceryList.js:

```
GroceryList.prototype.add = function(grocery) {
	var that = this;
	return new Promise(function(resolve, reject) {
		http.request({
			url: config.apiUrl + "Groceries",
			method: "POST",
			content: JSON.stringify({
				Name: grocery
			}),
			headers: {
				"Authorization": "Bearer " + config.token,
				"Content-Type": "application/json"
			}
		}).then(function() {
			that.push({ name: grocery });
			resolve();
		}).catch(function() {
			reject();
		});
	});
};
```

If you build and rerun your app now, you'll find that you can add a grocery item and it will appear immediately in your list.

todo: explain promises and how the VM is interacting with the M and bubbling back to the view.


 


