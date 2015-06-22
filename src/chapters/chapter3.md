## NativeScript modules

In the previous chapter, we already saw how NativeScript leverages the concept of 'modules' to include bits of code that are kept in the tns_modules folder. Using 'require', you can include these snippets ad hoc in your code when you need to use them, something like npm modules. Let's take a closer look at these modules and what they can do for us.

### Connecting to backends with the http module

You probably noticed that while we were busy constructing the frontend xml, the code-behind JavaScript file, the View Model, and the Model, that data was passing magically...somewhere. There's actually no magic involved, actually we have a config file that contains our API Key to Telerik Backend Services, a place where we are storing our users' information.

Take a look at app/shared/config.js. There's only a small code snippet there, but it includes a hard-coded API Key that we use throughout the app to access the backend (in real life, you would of course use your own API Key):

```
module.exports = {
	apiUrl: "http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/"
};
```

The config file is used in app/shared/models/User.js:

```
var config = require("../../shared/config");
```

and is used to construct a url via which we can post stringified JSON data via POST:

```
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
```

In the sample above, we use the 'http' module which we simply 'require' at the top of the User.js file:

```
var http = require("http");
```

If you dig a bit into the tns_modules folder and find the http folder, you can see how a tns module is constructed. It includes:
- a package.json that sets the name of the module and includes the base http.js file
- a file containing android native code (http-request.android.js) 
- a file containing ios native code (http-request.ios.js)
- a generic file (http.js) that abstract the platform-specific code above into a platform-agnostic format readable by the NativeScript runtime.

More information on modules can be found [here](http://developer.telerik.com/featured/nativescript-works/). You might try to write your own module!


### Dialog module

The dialog module is also used several times in our Groceries app. Its code is found in the tns_modules/ui folder with other UI widgets. To use this module, you have several options, including control over the buttons you include in the alert and their text, along with custom messaging in the alert itself:

```
dialogs.alert({
	message: "Unfortunately we were unable to create your account.",
	okButtonText: "OK"
});
```

### ListView

Let's leverage another UI module to craft a page to actually hold our grocery data. This is the page we want users to navigate to once they login, so let's uncomment line 20 in app/views/login/login-view-model.js to allow this navigation to happen:

```
frameModule.topmost().navigate("./views/list/list");
```

**Exercise: Construct the list view**

In app/views/list/list.xml, let's get started using the ListView module by creating a list where our groceries will reside:

```
<Page navigatedTo="navigatedTo">
	<GridLayout rows="auto, *" columns="*, *, *">
		<Border borderWidth="10" borderColor="#034793" row="0" colSpan="2">
			<TextField id="grocery" text="{{ grocery }}" hint="Enter a grocery item"/>
		</Border>

		<Button text="Add" tap="add" row="0" col="2"></Button>

		<ListView items="{{ groceryList }}" row="1" colSpan="3">
			<ListView.itemTemplate>
				<Label text="{{ name }}" horizontalAlignment="left"/>
			</ListView.itemTemplate>
		</ListView>
	</GridLayout>
</Page>
```

Note our use of the ListView module. In this case, we're not requiring a tns_module from the ui folder, but are rather using the ui widget within the xml code. This is a different way of leveraging these modules. If we wanted to, we could construct a ListView in pure JavaScript code behind the scenes as shown in [this example](http://docs.nativescript.org/ApiReference/ui/list-view/HOW-TO.html). However for our purposes, we can simply use xml to build the ListView and thereby follow the pattern we use in the login and register screens.


Let's go ahead and build the code-behind file as usual. In app/views/list/list.js, add:

```
var view = require("ui/core/view");
var viewModel = require("./list-view-model");
var page;

exports.navigatedTo = function(args) {
	page = args.object;
	if (page.ios) {
		page.ios.title = "Groceries";
	}

	page.bindingContext = viewModel;
	viewModel.reset();
};
```

There are a couple of things happening in this file. First, we have the function navigatedTo, which will set up our page for data binding when we navigate to it in the xml:

```
<Page navigatedTo="navigatedTo">
```

This function also includes some ios-specific code. This area of the app, as we'll find, includes a bit of platform-specific code so that we can provide a good adaptive experience for our users.

Now let's build out the View Model. In app/views/list/list-view-model.js, add:

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

You'll note that the display looks a little funny - the items in the list are pushed too far to the left, for example. Let's clean up the css for this page by populating app/views/list/list.css:

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

That's better!

<img src="images/list-view-2.png"/>

### Other modules

There are several modules that come out of the box with your NativeScript install, including a location service, a file-system helper, timer, camera, and even a color module that helps navigate the ways various colors are handled cross-platform. If you are interested in helping build and distribute more modules for the community, there's a [good guide](http://developer.telerik.com/featured/building-your-own-nativescript-modules-for-npm/) available on how to do this.

