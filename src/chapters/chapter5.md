## Using npm

Often, we need to include modules that are not by default available in tns_modules to allow for special functionality. We can leverage npm, node package manager, to import plugins and modules into our project. Let's add the [NativeScript Social Share widget](https://www.npmjs.com/package/nativescript-social-share) so that we can email our grocery lists.

### Using npm modules in your app

**Exercise: Install and use the Social Sharing widget**

Cd to the app directory in your Groceries project folder:

`cd Documents/NativeScript/Groceries/app/`

and install the module:

`npm install nativescript-social-share`

We're going to add this functionality to our list of groceries, so in /app/views/list/list.js, require the module:

`var socialShare = require("../../node_modules/nativescript-social-share/social-share");`

and add a function to share our list at the bottom of list.js:

```
exports.share = function() {
	socialShare.shareText("I love NativeScript!");
}
```

Add a button to click to share the list:

```
<Border borderWidth="1" borderColor="#034793" row="2" colSpan="3">
	<Button text="share" tap="share"/>
</Border>
```

Change the GridLayout to account for this button by editing the GridLayout tag at the top:

```
<GridLayout rows="auto, *, 40" columns="*, *, *">
```

Now when you run the app, you'll see a share button that, when clicked, allows you to email a list:

![share](images/share-button.png)

### Building your own NativeScript modules