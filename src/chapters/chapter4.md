## NativeScript modules

In the previous chapter, you saw how NativeScript leverages the concept of 'modules' to include bits of code that are kept in the `tns_modules` folder. Using `require()`, you can include these snippets ad-hoc in your code when you need to use them, similar to the way you use npm to import node libraries. Let's take a closer look at these modules and what they can do for your app.

>If you dig a bit into the tns_modules folder and find the http folder, you can see how a NativeScript module is constructed. It includes:
- a package.json file that sets the name of the module and includes the base file that defines the module.
- a file containing android native code (http-request.android.js).
- a file containing ios native code (http-request.ios.js).
- a generic file (http.js) that abstracts the platform-specific code above into a platform-agnostic API so that you can make HTTP calls on both platforms using a single JavaScript API.

>A note about platform-specific files: Any file, whether XML, CSS, or JavaScript, in NativeScript can have a platform-specific file and will be named accordingly. These files are included on an "as needed" basis when NativeScript compiles an app - for an iOS app, the ios file is included, and for Android, the android version is included. These files contain native code to manage device-specific actions, but a platform-specific file written in XML or CSS might contain code to make the presentation layer behave or look differently on a different platform. We'll explore this method in detail below.

More information on modules can be found [here](http://developer.telerik.com/featured/nativescript-works/).

### Connecting the model to the back end with the http module

You probably noticed, if you created your own user on the registration page, that data was passing magically...somewhere. There's actually no magic involved; there is a config file that contains an API Key to [Telerik BackEnd Services](http://www.telerik.com/backend-services), where we are storing our users' information. You don't have to use Telerik Backend Services; you can connect to any HTTP endpoint you like! The Groceries app just happens to use the Telerik endpoint.

Take a look at `app/shared/config.js`. There's only a small code snippet there, but it includes a hard-coded API Key that we use throughout the app to access the back end (in real life, you would of course use your own API Key):

```
module.exports = {
	apiUrl: "http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/"
};
```

Take a look in `app/shared/view-models`. Normally, models and view models use a service to connect to and communicate with a back end. In this case, the connection code is added directly into the view model for simplicity. You can see this demonstrated in the `user-view-model.js` file "register" function.

Note that the config file is used in all the model files, for example in User model: `app/shared/view-models/user-view-model.js`:

```
var config = require("../../shared/config");
```

The [http module](http://docs.nativescript.org/ApiReference/http/README.html) that you examined in the tns_modules folder is present as well. This module allows the app to communicate with an external endpoint over HTTP.

```
var http = require("http");
```

<h4 class="exercise-start">
    <b>Exercise</b>: Complete the login in the model
</h4>

To complete the login of a user, in `app/shared/view-models/user-view-model.js`, under the line where you require `config`, you need to require [the http module](http://docs.nativescript.org/ApiReference/http/README.html). This module allows the app to communicate with an external endpoint over HTTP.

```
var http = require("http");
```

Now you can build out the login function. Paste in the following code in between the login function brackets:

```
	var that = this;
	return new Promise(function(resolve, reject) {
		http.request({
			url: config.apiUrl + "oauth/token",
			method: "POST",
			content: JSON.stringify({
				username: viewModel.get("email_address"),
				password: viewModel.get("password"),
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
```
What's going on here? 

- You return a new Promise, which primarily allows the caller of this function to execute code after the asynchronous login either completes successfully or fails.

>**Promises**: [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) are a part of ECMAScript 6 (the scripting language of which JavaScript is an implementation) that have been implemented in Google's V8 engine and JavaScriptCore framework (which provides a JavaScript engine for WebKit) and are thereby available in NativeScript apps.

- Next, you use the http module's [`request()` method](http://docs.nativescript.org/ApiReference/http/HttpRequestOptions.html) to POST data to the `apiUrl` that is set up in config. The username, password and grant_type are sent to this endpoint as a JSON string. Telerik Backend Services [uses the parameter of grant_type](http://docs.telerik.com/platform/backend-services/development/rest-api/users/authenticate-user) for login.

- Finally, the endpoint's response is handled. `Http.request()` returns a Promise, which this code uses to either resolve or reject its own Promise. When the request is successful, the code saves a reference to the user's authentication token to be used on subsequent requests.

<div class="exercise-end"></div>

Now, instead of creating an observable in the `login.js`, you only need to create a new user view model object. This object is an observable, and it has the two functions you need to log users in and register them.

<h4 class="exercise-start">
	<b>Exercise: Add UserViewModel to login code-behind</b>
</h4>

In the `login.js` file, include a reference to the `shared/view-models/user-view-model`.

```
var UserViewModel = require("../../shared/view-models/user-view-model");
```

Now, instead of creating an observable, you only need to create a new user view model object. This object is an observable, and it has the two functions you need to log users in and register them.

```
var user = new UserViewModel({
	email_address: "user@domain.com",
	password: "password"
});
```

<div class="exercise-end"></div>

### Dialog module

The dialog module can be used to show [several types](http://docs.nativescript.org/ApiReference/ui/dialogs/HOW-TO.html) of popup UIs in your app, including action, confirm, alert, login, and prompt dialogs. It is a highly customizable module so that you can provide good prompts to your users, and it allows you to control the buttons you include in the alert, their text, and the messaging in the alert itself. Its code is found in the tns_modules/ui folder with other UI widgets.

<h4 class="exercise-start">
	<b>Exercise</b>: Handle an error with a dialog window
</h4>

Re-write your `signIn()` function to look like this:

```
exports.signIn = function() {
	user.login()
		.then(function() {
			
		}).catch(function() {
			dialogs.alert({
				message: "Unfortunately we could not find your account.",
				okButtonText: "OK"
			});
		});
};
```
<div class="exercise-end"></div>

What's happening here? In this case, the `login()` function uses the Promise approach that is returned by the login function in `app/shared/view-models/user-view-model.js` to handle both a successful and an unsuccessful login. If the login is unsuccessful, we show a dialog with a popup button stating that your account can't be found.

Now that you are able to handle an unsuccessful login, you need to allow people to view their grocery list after they successfully login. Once the user logs in, you can use the "list" page to facilitate adding and removing groceries to and from a list. To do that, you need a module that will show items in a list, which is exactly what the ListView does.

### ListView

Let's use another UI module to craft a page to actually hold our grocery data. This is the page we want users to navigate to once they login, so let's add a line in `app/views/login/login.js` to allow this navigation to happen. In the `signIn()` function you edited above, add the following line after `.then(function(){`:

```
frameModule.topmost().navigate("./views/list/list");
```

Now, if you rebuild and run your app in an emulator, you can login either using your credentials that you created earlier, or TJ's:

![login 6](images/login-stage6-ios.png)
![login 6](images/login-stage6-android.png)

You'll find that after you login, you're sent to a blank screen. You're going to build a list to hold grocery data next. 

<h4 class="exercise-start">
    <b>Exercise</b>: Construct the list view
</h4>

In `app/views/list/list.xml`, let's get started using the ListView module by creating a list where our groceries will reside:

```
<Page navigatedTo="navigatedTo">
	<GridLayout>		
		<ListView items="{{ groceryList }}">
			<ListView.itemTemplate>
				<Label text="{{ name }}" horizontalAlignment="left" verticalAlignment="center"/>
			</ListView.itemTemplate>
		</ListView>
	</GridLayout>
</Page>
```
<div class="exercise-end"></div>

>navigatedTo: The navigatedTo function that is called during the navigation event allows data to pass from one page to another when navigation is occurring. In this case, the navigatedTo function in `app/views/list/list.js` will set the page title for iOS, sets up the deletion module, sets up binding, and empties and reloads the list. You'll build this functionality below.

Note the use of the ListView module. In this case, we're not requiring a tns_module from the ui folder, but are rather using the ui widget within the xml code. This is a different way to use these modules. 

If you wanted to, you could construct a ListView in pure JavaScript code behind the scenes as shown in [this example](http://docs.nativescript.org/ApiReference/ui/list-view/HOW-TO.html). However for now, you can simply use xml to build the ListView and thereby follow the pattern you use in the login and register screens.

Finally, note the ListView's use of an itemTemplate. Using an itemTemplate gives you more control over how the actual items look within the list.

>Tip: Other modules
There are several modules that come out of the box with your NativeScript install, including a location service, a file-system helper, timer, camera, and even a color module that helps navigate the ways various colors are handled cross-platform. If you are interested in helping build and distribute more modules for the community, there's a [good guide](http://developer.telerik.com/featured/building-your-own-nativescript-modules-for-npm/) available on how to do this.

If you were to run this code as it stands, you wouldn't see any items in the grocery list. You need to build out a way to manage data within the ListView module.

### Working with arrays

To be able to manage data in the grocery list, you need to build a connection between the presentation tier and the database as you did for login. Go ahead and build out these pieces in your grocery list.

<h4 class="exercise-start">
    <b>Exercise</b>: Populate the list view
</h4>

In app/views/list/list.js, add:

```
var dialogs = require("ui/dialogs");
var observableModule = require("data/observable");
var view = require("ui/core/view");
var GroceryList = require("../../shared/view-models/grocery-list-view-model");

var page;

var groceryList = new GroceryList([
	{ name: "eggs" },
	{ name: "bread" },
	{ name: "cereal" }	
]);

var pageData = new observableModule.Observable({
		groceryList: groceryList
});

exports.navigatedTo = function(args) {
    page = args.object;
    page.bindingContext = pageData;
};
```

Here, you're creating a new observable object called "pageData" which will be the object that the UI is bound to. Inside, you are setting the "groceries" property to be a new instance of GroceryList view model, which was required in at the top of the file. 

Examine the contents of the GroceryList view model in the file `app/shared/view-models/grocery-list-view-model.js`.

```
var config = require("../../shared/config");
var http = require("http");
var observableArray = require("data/observable-array");

function GroceryList(items) {
	var viewModel = new observableArray.ObservableArray(items);
	return viewModel;
}

module.exports = GroceryList;
```

Note that the GroceryList view model uses the "ObservableArray" module. This is another tns_module that is specifically for working with collections of objects and values. You can nest observable arrays inside of observable objects, just as you are currently doing with "pageData".

Now if you were to run this code in the emulator, you would see a list of your hard-coded values:

<>images

Instead of a static list of items, you need to load up any grocery items that exist in the database to which you just logged in. Modify the code in `app/views/list/list.js` to clear out any existing items out of the groceries list and load in new ones using a "load" method.

```
var dialogs = require("ui/dialogs");
var observableModule = require("data/observable");
var view = require("ui/core/view");
var GroceryList = require("../../shared/view-models/grocery-list-view-model");

var page;

var groceryList = new GroceryList([]);

var pageData = new observableModule.Observable({
	groceryList: groceryList
});

exports.navigatedTo = function(args) {
    page = args.object;
    page.bindingContext = pageData;

    groceryList.clear();
    groceryList.load();
};
```

In this code, groceryList is now referencing the grocery list model and in the navigatedTo function, the groceryList object is emptied and then reloaded from the database so that the data remains current.  
 
Now you should build out the `clear()` and `load()` functions in the model so that we can get this data from the database.

In `app/shared/view-models/grocery-list-view-model.js`, grab any Grocery data that might exist in the back end and push it into the grocery array:

```
function GroceryList(items) {

	var viewModel = new observableArray.ObservableArray(items);
	
	viewModel.load = function() {
		http.getJSON({
			url: config.apiUrl + "Groceries",
			method: "GET",
			headers: {
				"Authorization": "Bearer " + config.token
			}
		}).then(function(data) {
			
			data.Result.forEach(function(grocery) {
				viewModel.push({
					name: grocery.Name,
					id: grocery.Id
				});
			});
		});
	}

	viewModel.clear = function() {
		while (viewModel.length) {
			viewModel.pop();
		}
	}

	return viewModel;
}
```
<div class="exercise-end"></div>

In the load function above, you are pulling down the list associated to the user's credentials. In the empty function, you clear out the array and get ready to reload it. If you rebuild, run the app, and login as tj.vantoll@gmail.com, you'll find a list of groceries pulled from Backend Services in the Groceries data type. It will look something like this:

![list ios](images/list-ios.png)
![list android](images/list-android.png)

It's great that you can see data already in the database, but you also need to add some items. Go ahead and build that functionality.

<h4 class="exercise-start">
    <b>Exercise</b>: Add the ability to create a Grocery item
</h4>

First, add another parameter to the pageData observable object as an empty placeholder in `app/views/list/list.js` above the line: `exports.navigatedTo = function(args) {`

```
pageData.set("grocery", "");
```

Edit the list view xml to allow for a two column layout with a text field and a button to add grocery items. Change the GridLayout in `app/views/list/list.xml` initial tag to account for this layout:

```
<GridLayout rows="auto, *" columns="*, *, *">
```
>This layout will include two rows, one auto-sized according to its children's dimensions, and the other stretchable to allow for the expanding list. It will have three auto-stretching columns as the text field will occupy two columns and the button one column.

Add a text field and a button to have a place to enter grocery items. The text field will have the id 'grocery' and is bound to the grocery object. It has a hint that will show on the front end to help the user, and will occupy one row and two columns. The button will have a tap event - `tap` and will occupy the initial row as well and the third column - remember numbering of rows and columns starts at zero. Add these two lines after the GridLayout initial tag:

```
	<TextField id="grocery" text="{{ grocery }}" hint="Enter a grocery item" row="0" colspan="2" />
	<Button text="Add" tap="add" row="0" col="2"></Button>
```
Finally, edit the `<ListView>` tag to ensure that it will expand across the new columns you created:

```
<ListView items="{{ groceryList }}" id="groceryList" row="1" colSpan="3">
```

Now you are ready to build the add tap event.

In app/views/list/list.js, add a function to respond to the 'add' tap event that you just added. You'll put this underneath the navigatedTo function.

```
exports.add = function() {
    //check for empty submission
    if (pageData.get("grocery").trim() !== "") {
        //dismiss the keyboard
        view.getViewById(page, "grocery").dismissSoftInput();
        groceryList.add(pageData.get("grocery"))
            .catch(function() {
                dialogs.alert({
                    message: "An error occurred adding to your list.",
                    okButtonText: "OK"
                });
            });
        //empty the input field
        pageData.set("grocery", "");
    } else {
        dialogs.alert({
            message: "Please enter a grocery item",
            okButtonText: "OK"
        });
    }
};
```
In this function, you test for an empty value, and if this test passes, dismiss the keyboard, then get the 'grocery' item from the input field and add it to the groceryList object. 

Finally, let the manipulation of this new data be handled in the model by adding the following function under the 'empty' function, but before the view model is returned in `/app/shared/view-models/grocery-list-view-model.js`

```
viewModel.add = function(grocery) {
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
			viewModel.push({ name: grocery });
			resolve();
		}).catch(function() {
			reject();
		});
	});
};
```
<div class="exercise-end"></div>

If you build and rerun your app now, you'll find that you can add a grocery item and it will appear immediately in your list.

Now that you have login, registration, and list routines complete, you can enhance the app's functionality as a grocery list management tool. You can use the NativeScript Social Sharing plugin to get the data you input into an email so you can send yourself a reminder to pick up your groceries.
