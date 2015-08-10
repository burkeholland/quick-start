## Accessing native APIs

The beauty of NativeScript is that you can write a native iOS or Android app in JavaScript, XML, and CSS without touching Swift, Objective-C, or Java, if you choose. But what if you want to present a different, more platform-specific UI to your users?

NativeScript gives you the option to dig into native code as needed and to do so without leaving JavaScript. To show how this works in action, let's add some color to the ActionBar in the iOS version of the Groceries app, and change the color of the fonts and status bar to customize the look a bit on iOS.

### Customize the ActionBar - iOS

To create a really native look and feel for the action bar on iOS, you need to work with lower-level classes in the UINavigationController code. It's useful to reference [the iOS docs](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationController_Class/) when working in this level of the code.

In addition, you can view the mappings to these customizations that NativeScript provides [here](https://github.com/NativeScript/NativeScript/blob/master/ios.d.ts). In this file, you can search for 'UINavigationBar' and observe the classes you can work with to customize the iOS navigation bar.

<h4 class="exercise-start">
    <b>Exercise</b>: Modify the ActionBar items
</h4>

To access the internals of the ActionBar, import the frame module where the navigation resides.

In `app/views/list/list.js`, add the following line at the top of the file, under var view:

```
var frameModule = require("ui/frame");
```
Then, create variables to hold the controller and the ios navigationBar by adding the following lines to the top, under `pageData.set("groceryList", groceryList);`:

```
var controller = frameModule.topmost().ios.controller;
var navigationBar = controller.navigationBar;
```

The controller holds a reference to the iOS controller within the frameModule's topmost frame, and the navigationBar is referenced from within the ios controller.

Finally, add some customizations to the navigationBar for iOS in the codeblock in the `exports.navigatedTo()` function:

```
if (page.ios) {
	navigationBar.barTintColor = UIColor.colorWithRedGreenBlueAlpha(0.011, 0.278, 0.576, 1);
	navigationBar.titleTextAttributes = new NSDictionary([UIColor.whiteColor()], [NSForegroundColorAttributeName]);
	navigationBar.barStyle = 1;
	navigationBar.tintColor = UIColor.whiteColor();
}		
```
>Note, you can fork the user's platform-specific experience by leveraging page attributes, specifying `ios` or `android`

<div class="exercise-end"></div>


![actionbar](images/actionbar-ios.png)


Now if you emulate the app for iOS, you'll see the new colors appear in the ActionBar. In the above code, notice how you access native iOS APIs from JavaScript. NativeScript does the abstraction for you to make it easy.

Forking the user experience can entail more than just changing some colors.
Sliding to delete list items is a common UI interaction on iOS. But since you want to make this app feel as native as possible, it's better to fork your code at this point to provide a more platform-specific experience. So to enable a user to delete an item from a list, we're going to create a slide-to-delete UI for iOS, and use an Android-style 'trash can' icon for Android.

### Deleting from a list - Android

Earlier, you saw how any element in a NativeScript app can have Android vs. iOS attributes. One way is to add such an attribute directly into the xml presentation tier code: `ios.position="right"`. Another is by creating separate .js, .css, or .xml files, such as using a `page.android.xml` file for your Android UI, and `page.ios.xml` for your iOS UI. To create a separate delete mechanism for this app, you will use both of these strategies.

<h4 class="exercise-start">
    <b>Exercise</b>: Edit the List View
</h4>

For Android, you are going to add an icon that will be hidden on iOS. To account for this button, you also need to edit the layout of the ListView, so replace the current ListView code in `app/views/list/list.xml` with the following:

```
<ListView items="{{ groceryList }}" id="groceryList" row="1" colSpan="3">
	<ListView.itemTemplate>
		<GridLayout columns="*, auto">
			<Label text="{{ name }}"/>
			<Image src="res://ic_menu_delete" ios:visibility="collapsed" col="1" tap="delete"/>
		</GridLayout>
	</ListView.itemTemplate>
</ListView>
```
In this code, you have created:

- an itemTemplate, in which you can format the rows of your list view. In this itemTemplate you have nested a GridLayout with two columns, one stretched and one expanding only to the width of the child. 
- a label that contains the name of the grocery item is added to the first column and aligned left. 
- an image, the delete icon, that is hidden from ios devices. The attribute `ios.visibility` sets the Image's visibility CSS property to "collapsed", which hides it. Because the attribute was prefixed with "ios:", that CSS property is only applied on iOS; therefore the button displays on Android devices, but not on iOS ones. This icon is placed in the right-hand column, and given a tap event. The image itself has already been placed in the app for you, and can be found in appropriate sizes in the four drawable folders in `/app/App_Resources/Android/drawable-*dpi`.

<div class="exercise-end"></div>

![delete](images/delete-ios.png)
![delete](images/delete-android.png) 

Now that you have a trash can icon appear within the grocery list view, you need to add the delete() functions in the model and view model:

<h4 class="exercise-start">
    <b>Exercise</b>: Build the delete functions
</h4>

First, create a `delete()` function in `app/views/list/list.js` at the bottom of this file, under the `share()` function:

```
exports.delete = function(args) {
	var item = args.view.bindingContext;
	var index = groceryList.indexOf(item);
	groceryList.delete(index);
};
```
This code reads in the item that is tapped via the arguments passed into the function, matches that item to the index in the groceryList object, and sends that index to be deleted. It remains only to implement the actual deletion in the model.

In `app/shared/models/GroceryList.js`, add a function to delete an item:

```
GroceryList.prototype.delete = function(index) {
	var that = this;
	return new Promise(function(resolve, reject) {
		http.request({
			url: config.apiUrl + "Groceries/" + that.getItem(index).id,
			method: "DELETE",
			headers: {
				"Authorization": "Bearer " + config.token,
				"Content-Type": "application/json"
			}
		}).then(function() {
			that.splice(index, 1);
			resolve();
		}).catch(function() {
			reject();
		});
	});
};

```
Similar to code elsewhere in the models, as you have seen, an item is passed to the function and a Promise is returned. Data is sent to Backend Services, including a URL with the id of the item to be deleted appended, the method (DELETE), and headers which include the token saved after the user logs in. If the deletion is successful, the item is removed from the grocery list observable that feeds the list view. 
<div class="exercise-end"></div>

Now that you have built the interface for Android's tappable icon, let's add a way for a swipe delete interface to be shown on iOS.

### Deleting from a list - iOS

There is already a module ready-built for you to use to enable a swipe-to-delete gesture within a list view. Take a look in `shared/utils/ios-swipe-delete.js`. You'll find the Objective-C class `UITableViewDataSource` instantiated and a number of its properties are set, including `tableViewNumberOfRowsInSection`, `tableViewCanEditRowAtIndexPath`, etc, to build up the EditableDataSource that is used to build the editable list for iOS.

<h4 class="exercise-start">
    <b>Exercise</b>: Edit the List View
</h4>

Include this library into `app/views/list/list.js` at the top, under the inclusion of the social share plugin:

```
var swipeDelete = require("../../shared/utils/ios-swipe-delete");
```
Then, add this code to the `navigatedTo()` function under `page = args.object;`:
```
	var listView = view.getViewById(page, "groceryList");
	swipeDelete.enable(listView, function(index) {
		groceryList.delete(index);
	});
``` 

<div class="exercise-end"></div>

That's all you have to do to enable swipe-to-delete for iOS! What this code does is use this functionality from the included utility, invoking the `enable()` function from the swipeDelete utility for the referenced listView, and flagging the index of the list to be deleted.



>Learn more about how the NativeScript API leverages native code [here](http://developer.telerik.com/featured/nativescript-works/)

You've created a functional, cross-platform app to manage your grocery list! In the process you created a unique UI for Android and iOS, leveraged plugins and npm modules, learned how to login and register, managed backend services, create a functioning list with add and delete options, and more. Congratulations!

