## MVVM

### What is MVVM?

In this chapter, we'll learn about the base pattern on which the NativeScript framework is built, MVVM, or "Model, View, View Model". We've already seen several examples of the elements that are used in this design pattern:

- Model (like Users.js which handles the API call to the backend to login and register students). The model represents the data. Separating the model from the various views that may use it allows for code reuse.
- View (like the register.xml file we built). The view is often data-bound to the View Model so that changes are instantly represented on the presentation tier.
- View Model (like the register-view-model.js file that takes data from the frontend and passes it to the Model for processing). The View Model exposes the models and logic needed by the view, and handles presentation logic.

We can leverage this pattern to provide two-way data-binding in our app. 

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

### Models, Views, and ViewModels

...


### Data Binding

...