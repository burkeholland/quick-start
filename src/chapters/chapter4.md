## NativeScript modules

In the previous chapter, you already saw how NativeScript leverages the concept of 'modules' to include bits of code that are kept in the tns_modules folder. Using 'require', you can include these snippets ad hoc in your code when you need to use them, similar to the way you use npm to import node libraries. Let's take a closer look at these modules and what they can do for your app.

>If you dig a bit into the tns_modules folder and find the http folder, you can see how a tns module is constructed. It includes:
- a package.json that sets the name of the module and includes the base http.js file
- a file containing android native code (http-request.android.js) 
- a file containing ios native code (http-request.ios.js)
- a generic file (http.js) that abstract the platform-specific code above into a platform-agnostic format readable by the NativeScript runtime.

>More information on modules can be found [here](http://developer.telerik.com/featured/nativescript-works/).

### Connecting the model to the back end with the http module

You probably noticed, if you created your own user on the registration page, that data was passing magically...somewhere. There's actually no magic involved; there is a config file that contains an API Key to [Telerik Back End Services](http://www.telerik.com/backend-services), where we are storing our users' information. You don't have to use Telerik Backend Services; you can connect to any back end endpoint you like! The Groceries app just happens to use the Telerik endpoint.

Take a look at `app/shared/config.js`. There's only a small code snippet there, but it includes a hard-coded API Key that we use throughout the app to access the back end (in real life, you would of course use your own API Key):

```
module.exports = {
	apiUrl: "http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/"
};
```

Take a look in `app/shared/models`. Normally, models use a service to connect to and communicate with a back end. In this case, the connection code is added directly into the model for simplicity. You can see this demonstrated in the registration function.

Note that the config file is used in all the model files, for example in User model: `app/shared/models/User.js`:

```
var config = require("../../shared/config");
```

<h4 class="exercise-start">
    <b>Exercise</b>: Complete the login in the model
</h4>

To complete the login of a user, in `app/shared/Models/Users.js`, under the line where you require `config`, you need to require [the http module](http://docs.nativescript.org/ApiReference/http/README.html). This module allows the app to community to an external endpoint over http.

```
var http = require("http");
```

add a login function under `User.prototype = new observableModule.Observable();`:

```
User.prototype.login = function() {
	var that = this;
	return new Promise(function(resolve, reject) {
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
		}).then(function(data) {
			config.token = data.content.toJSON().Result.access_token;
			resolve();
		}).catch(function() {
			reject();
		});
	});
};
```
What's going on here? 

- First, you alias the 'this' variable by assigning it to a private variable, 'that'. This allows you to continue to access the original value of 'this' in inner functions

- Second, you return a new Promise, which creates an asynchronous request to an external endpoint
>**Promises**: Promises are a part of ECMAScript 6 (the scripting language of which JavaScript is an implementation) that have been implemented in Google's V8 engine and JavaScriptCore framework (which provides a JavaScript engine for WebKit) and are thereby available in NativeScript apps.

- Next, you use the http module to POST data to the apiUrl that is set up in config. The username and password is sent as a JSON string

- Finally, the endpoint's response is handled

Finally, allow the view model to invoke the login function that you just added to the model. In `app/views/login/login.js` rewrite your signIn function to look like this:

```
exports.signIn = function() {
	user.login()
		.then(function() {
			frameModule.topmost().navigate("./views/list/list");
		}).catch(function() {
			dialogs.alert({
				message: "Unfortunately we could not find your account.",
				okButtonText: "OK"
			});
		});
};
```
<div class="exercise-end"></div>

Now, if you rebuild and run your app in an emulator, you can login either using your credentials that you created earlier, or TJ's:

![login 6](images/login-stage6-ios.png)
![login 6](images/login-stage6-android.png)

You'll find that after you login, you're sent to a blank screen. You're going to build a list to hold grocery data next. But since we started discussing modules, let's take a look at how those work in NativeScript before continuing. 


### Dialog module

The dialog module is also used several times in our Groceries app. Its code is found in the tns_modules/ui folder with other UI widgets. To use this module, you have several options, including control over the buttons you include in the alert and their text, along with custom messaging in the alert itself:

```
dialogs.alert({
	message: "Unfortunately we were unable to create your account.",
	okButtonText: "OK"
});
```

### ListView

Let's use another UI module to craft a page to actually hold our grocery data. This is the page we want users to navigate to once they login, so let's add a line in app/views/login/login.js to allow this navigation to happen. In the signIn function, add the following line after '.then(function(){':

```
frameModule.topmost().navigate("./views/list/list");
```

<h4 class="exercise-start">
    <b>Exercise</b>: Construct the list view
</h4>

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
<div class="exercise-end"></div>

Note the use of the ListView module. In this case, we're not requiring a tns_module from the ui folder, but are rather using the ui widget within the xml code. This is a different way to use these modules. 

If you wanted to, you could construct a ListView in pure JavaScript code behind the scenes as shown in [this example](http://docs.nativescript.org/ApiReference/ui/list-view/HOW-TO.html). However for now, you can simply use xml to build the ListView and thereby follow the pattern you use in the login and register screens.

Finally, note the ListView's use of an itemTemplate. Using an itemTemplate gives you more control over how the actual items look within the list.

### Other modules

There are several modules that come out of the box with your NativeScript install, including a location service, a file-system helper, timer, camera, and even a color module that helps navigate the ways various colors are handled cross-platform. If you are interested in helping build and distribute more modules for the community, there's a [good guide](http://developer.telerik.com/featured/building-your-own-nativescript-modules-for-npm/) available on how to do this.

