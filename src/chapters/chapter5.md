## Using npm

Often, we need to include modules that are not by default available in tns_modules to allow for special functionality. We can leverage npm, node package manager, to import plugins and modules into our project. Let's add the [NativeScript Social Share widget](https://www.npmjs.com/package/nativescript-social-share) so that we can email our grocery lists.

### Using npm modules in your app

**Exercise: Install and use the Social Sharing widget**

Cd to the app directory in your Groceries project folder:

```
cd Documents/NativeScript/Groceries/app/
```

and install the module:

```
npm install nativescript-social-share
```

We're going to add this functionality to our list of groceries, so in /app/views/list/list.js, require the module:

```
var socialShare = require("../../node_modules/nativescript-social-share/social-share");
```
Let's make an area at the top of our list file to show a link to share a grocery list. Under the <Page> tag, add the following code. Tapping this link will open a native email sharing widget:

```
<Page.optionsMenu>
	<MenuItem text="Share" tap="share" android.position="actionBar"/>
</Page.optionsMenu>
```

Now we need to get our grocery list into a comma-delimited format that will be fed to the socialSharing widget. To do this, add a function to return our list at the bottom of list-view-model.js:

```
ListViewModel.prototype.getList = function() {
	var groceryList = this.get("groceryList");
	var list = [];
	for (var i = 0, size = groceryList.length; i < size ; i++) {
		list.push(groceryList.getItem(i).name);
	}
	return list.join(",");
};
```
And then finally add a function in app/views/list/list.js to call the socialSharing widget:

```
exports.share = function() {
	var list = viewModel.getList();
	socialShare.shareText(list);
}
```

Now when you run the app, you'll see a Share button at the top that, when clicked, allows you to email a list using a native interface:

![share](images/share-view.png)
![share](images/share-email.png)

It's very cool to add ready-built modules to your app. But maybe you want to build your own! 

### Building your own NativeScript modules