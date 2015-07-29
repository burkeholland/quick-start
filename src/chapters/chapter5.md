## Plugins and npm modules

Often, you need to include plugins and/or modules that are not by default available in the `tns_modules` folder to enable special functionality in your app. You can leverage [npm](https://www.npmjs.com/), node package manager, to import plugins and modules into your project. Alternately, you can install NativeScript plugins, which are simply npm modules that can access native code and leverage Android and iOS SDKs if required. 

In this section, you'll install and use an external module, an email validator, so that you can check email addresses for validity as they are entered in the login screen. Then, you'll add a NativeScript plugin, the [NativeScript Social Share widget](https://www.npmjs.com/package/nativescript-social-share), to manage sharing grocery lists. 

### Using a npm module in your app

It would be nice to be able to make sure people are entering well-formatted email addresses into your app in the registration screen, so you can use a basic npm module from the npm registry to avoid having to write this functionality yourself. You can [use this validator](https://www.npmjs.com/package/email-validator) to test for valid addresses.

<h4 class="exercise-start">
    <b>Exercise</b>: Install and use an email validator
</h4>

Make sure that you are working in the root directory in your Groceries project folder, a.k.a. here:

my-project <----------------
    ├── app
    │   └── ...
    ├── package.json
    └── platforms
        ├── android
        └── ios 

and install the module:

```
npm install email-validator --save
```
What just happened? The install process does a few things in the background. First, it adds a line to `app/package.json` because you added the `--save` flag to the above command:

```
"dependencies": {
		"email-validator": "^1.0.2"
	}
```
Then, the NativeScript CLI adds a folder to the `node_modules` in the root called `email-validator`. In this folder is the code used by the module, in this case a small regex check in `app/node_modules/email_validatorindex.js`. 

You're going to add this functionality to our list of groceries, so in `/app/shared/models/User.js`, require the module:

```
var validator = require("email-validator/index");

```

>In this case, we need to explicitly point to the index.js file, because normally NativeScript is configured to look in an npm module's package.json file for the "main" value to reference a file, like `index.js`. In the case of this particular module, the `main` value is simply `index` so you need to reference the main file directly.

To make use of this validator, add a function to `app/models/User.js` under the register function:

```
User.prototype.isValidEmail = function() {
	var email = this.get("email_address");
	return validator.validate(email);
};
```
Then, edit the registration function in `app/views/register/register.js` to trap any malformed email addresses:

```
exports.register = function() {
	if (user.isValidEmail()) {
		completeRegistration();
	} else {
		dialogs.alert({
			message: "Please include a valid email address.",
			okButtonText: "OK"
		});
	}
};
```
In this code, the user submits an email and password, and the value is sent to the model for validation. If it passes, registration can proceed, otherwise an alert is shown:

![share](images/email-validate-ios.png)
![share](images/email-validate-android.png) 

<div class="exercise-end"></div>

### Using a NativeScript plugin in your app

In order to email grocery lists via your app, you need to install the Social Sharing plugin, with which you can share both text and images. 

<h4 class="exercise-start">
    <b>Exercise</b>: Install and use the Social Sharing widget
</h4>

To install this plugin, all you need to do is type:

```
tns plugin add nativescript-social-share
```
What just happened? The install process does the same thing that an `npm install` command does, in that it writes the dependency to `package.json`, and it also configures any native code that the plugin needs to use. 

Now, include the social share plugin at the top of `app/views/list/list.js` using `require()`:

```
var socialShare = require("nativescript-social-share");
```

Now, make an area at the top of `app/views/list/list.xml` file to show a link to share a grocery list. Under the <Page> tag, add the following code. Once you build the `share()` function, tapping this link will open a native email sharing widget:

```
<Page.actionBar>
		<ActionBar title="Groceries">
			<ActionBar.actionItems>
				<ActionItem text="Share" tap="share" ios.position="right" />
			</ActionBar.actionItems>
		</ActionBar>
</Page.actionBar>
```
You have now added an [ActionBar](https://docs.nativescript.org/ApiReference/ui/action-bar/ActionBar), which is a UI component that includes various menu items, enclosed in the `<ActionBar.actionItems>` tag. The title of the ActionBar allows page-specific titles to be displayed.

>Note, on iOS devices, action bar items are placed from left to right in sequence, so you can override that by specifying an icon's position.

Now you need to get your grocery list into a comma-delimited format and feed it to the socialSharing widget. To do this, add a function to return our list at the bottom of `/app/views/list/list.js`:

```
exports.share = function() {
	var list = [];
	var finalList = "";
	for (var i = 0, size = groceryList.length; i < size ; i++) {
		list.push(groceryList.getItem(i).name);
	}
	var listString = list.join(", ").trim();
	socialShare.shareText(listString);
};
```
With this code, you are taking the `name` key of your groceryList object and converting it to a comma-delimited string to use in an email.

<div class="exercise-end"></div>


Now when you run the app, you'll see a Share button at the top that, when clicked, allows you to email a list using a native interface:

![share](images/share-view-ios.png)
![share](images/share-email-ios.png)
![share](images/share-view-android.png)
![share](images/share-email-android.png)

You can learn more about modules and plugins [here](https://www.nativescript.org/blog/using-npm-modules-and-nativescript-plugins).

>**Tip:** It's very cool to add ready-built modules to your app. You can search for NativeScript plugins [here](https://www.npmjs.com/search?q=nativescript) and find a good variety of supported npm modules [here](https://github.com/NativeScript/NativeScript/wiki/supported-npm-modules). But maybe you want to build your own! Read more on how to do this [here](http://developer.telerik.com/featured/building-your-own-nativescript-modules-for-npm/).

