## Advanced NativeScript

So far, we've been working with some fairly standard forms and some common use cases for getting a mobile app up and running with NativeScript, including registering for and logging into a backend service. Now it's time to dig a little deeper and turn this app into a grocery list app. In this chapter we'll construct a listView that will accept items into a list and save them in the backend database.

### The View Model and Model


To be able to manage data in the grocery list, we need to build a connection between the presentation tier and the database as we did for login. Let's build out these pieces in our grocery list.

<h4 class="exercise-start">
    <b>Exercise</b>: Construct the list View Model and Model
</h4>

In app/views/list/list.js, add:

```
var GroceryList = require("../../shared/models/GroceryList");

var page;
var pageData = new observable.Observable();
var groceryList = new GroceryList();

pageData.set("grocery", "");
pageData.set("groceryList", groceryList);

exports.navigatedTo = function(args) {
	page = args.object;
	if (page.ios) {
		page.ios.title = "Groceries";
		var listView = view.getViewById(page, "groceryList");
		swipeDelete.enable(listView, function(index) {
			groceryList.delete(index);
		});
	}

	page.bindingContext = pageData;
	groceryList.empty();
	groceryList.load();
};

```

Here, we're creating a new empty grocery array and a groceryList variable to instantiate our GroceryList object that we create in the Model. We're also emptying and refilling our groceryList in the navigatedTo function which we added to the xml previously in `app/views/list/list.xml`. Let's create the GroceryList object in the Model:

In app/shared/models/GroceryList.js, let's grab any Grocery data that might exist in the backend and push it into the grocery array:

```
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
			that.push({
				name: grocery.Name,
				id: grocery.Id
			});
		});
	});
};
GroceryList.prototype.empty = function() {
	while (this.length) {
		this.pop();
	}
};
```
<div class="exercise-end"></div>

In the load function above, we are pulling down the list associated to the user's credentials. In the empty function, we clear out the array and get ready to reload it. If you rebuild, run the app, and login as tj.vantoll@gmail.com, you'll find a list of groceries pulled from Backend services in the Groceries data type. It will look something like this:

![list ios](images/list-ios.png)
![list android](images/list-android.png)

It's great that we can see data already in the database, but we also need to add some items. Let's build that functionality.

<h4 class="exercise-start">
    <b>Exercise</b>: Add the ability to create a Grocery item
</h4>

Notice at the top of the list is a text area and an 'Add' button. Right now, it doesn't do anything. Let's fix that.

In app/views/list/list.js, add a function to respond to the 'add' tap event that is already in list.xml. We'll put this underneath the navigatedTo function.

```
exports.add = function() {
	view.getViewById(page, "grocery").dismissSoftInput();
	groceryList.add(pageData.get("grocery"))
		.catch(function() {
			dialogs.alert({
				message: "An error occurred adding to your list.",
				okButtonText: "OK"
			});
		});
	pageData.set("grocery", "");
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
<div class="exercise-end"></div>

If you build and rerun your app now, you'll find that you can add a grocery item and it will appear immediately in your list.

###Form Validation

You may have noticed that you can get away with submitting a blank list item form. Let's add a bit of form validation to stop a blank email address from being submitted.

<h4 class="exercise-start">
    <b>Exercise</b>: Add validation to the list input
</h4>

You shouldn't be able to input a blank item into your grocery list, so let's add some form validation. This validation will simply check if a field has been left blank.

To do this, edit the `add` function in `app/views/list/list.js` to first check for a blank entry, and then to allow the item to be added:

```
exports.add = function() {
	if (groceryList.isValidItem(pageData.get("grocery"))) {
		view.getViewById(page, "grocery").dismissSoftInput();
		groceryList.add(pageData.get("grocery"))
			.catch(function() {
				dialogs.alert({
					message: "An error occurred adding to your list.",
					okButtonText: "OK"
				});
			});
		pageData.set("grocery", "");
	} else {
		alert("Please enter a grocery item");
	}
};
```
Then, edit the Model, adding a function at the end of `app/shared/models/GroceryList.js` that will test for a blank item being sent to the backend:

```
GroceryList.prototype.isValidItem = function(grocery) {
	return !!(grocery);
};
```
<div class="exercise-end"></div>

Now that we have login, registration, and list routines complete, we need to work on the app's actual functionality as a grocery list management tool. You can use the NativeScript Social Sharing plugin to get the data you input into an email so you can send yourself a reminder to pick up your groceries.