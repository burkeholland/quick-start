## Using npm

Often, you need to include modules that are not by default available in tns_modules to allow for special functionality. You can leverage npm, node package manager, to import plugins and modules into our project. Add the [NativeScript Social Share widget](https://www.npmjs.com/package/nativescript-social-share) so that you can email your grocery lists.

### Using npm modules in your app

<h4 class="exercise-start">
    <b>Exercise</b>: Install and use the Social Sharing widget
</h4>

Cd to the app directory in your Groceries project folder:

```
cd Documents/NativeScript/Groceries/app/
```

and install the module:

```
npm install nativescript-social-share
```

We're going to add this functionality to our list of groceries, so in `/app/views/list/list.js`, require the module:

```
var socialShare = require("nativescript-social-share");

```
Let's make an area at the top of our list file to show a link to share a grocery list. Under the <Page> tag, add the following code. Tapping this link will open a native email sharing widget:

```
<Page.optionsMenu>
	<MenuItem text="Share" tap="share" android.position="actionBar"/>
</Page.optionsMenu>
```

Now you need to get our grocery list into a comma-delimited format and feed it to the socialSharing widget. To do this, add a function to return our list at the bottom of `/app/views/list/list.js`:


```
exports.share = function() {
	var list = [];
	var finalList = "";
	for (var i = 0, size = groceryList.length; i < size ; i++) {
		list.push(groceryList.getItem(i).name);
	}
	var listString = list.join(", ").trim();
	if (listString.substr(0,1) == ",") {
        finalList = listString.substring(1);
    }
	socialShare.shareText(finalList);
};
```
<div class="exercise-end"></div>

Now when you run the app, you'll see a Share button at the top that, when clicked, allows you to email a list using a native interface:

![share](images/share-view.png)
![share](images/share-email.png)

It's very cool to add ready-built modules to your app. But maybe you want to build your own! 

### Building your own NativeScript modules