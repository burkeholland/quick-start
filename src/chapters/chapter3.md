## NativeScript modules

In the previous chapter, we already saw how NativeScript leverages the concept of 'modules' to include bits of code that are kept in the tns_modules folder. Using 'require', you can include these snippets ad hoc in your code when you need to use them, similar to the way we use npm to import node libraries. Let's take a closer look at these modules and what they can do for us.

### Navigation and the frame module

While our Groceries app doesn't use complex navigation strategies, you have several available to you out of the box:

[TabView](http://docs.nativescript.org/ui-views#tabview)  
[SegmentedBar](http://docs.nativescript.org/ui-views#segmentedbar)

**Exercise: Enable the "Sign Up" button on the login screen with a navigational change**

Right now, if you were to click the Sign Up For Groceries button on the login screen, nothing would happen. Let's get this button to change the screen to show a registration form. 

In app/views/login/login.js, add the following function under the signIn function:

```
exports.register = function() {
	var topmost = frameModule.topmost();
	topmost.navigate("./views/register/register");
};

```
This function makes use of the module 'frameModule' which looks for the topmost frame and navigates to it. Here, we tell the topmost frame to navigate to the register view. If you run your code in an emulator, you'll find that you can now navigate to your registration view by clicking the "Sign Up" button.

Learn more about how to link up your navigational strategies [here](http://docs.nativescript.org/navigation#navigation).


### Connecting to a backend with the http module

You probably noticed that while we were busy constructing the frontend xml and the code-behind JavaScript file, data was passing magically...somewhere. There's actually no magic involved; we have a config file that contains our API Key to [Telerik Backend Services](http://www.telerik.com/backend-services), a place where we are storing our users' information.

Take a look at app/shared/config.js. There's only a small code snippet there, but it includes a hard-coded API Key that we use throughout the app to access the backend (in real life, you would of course use your own API Key):

```
module.exports = {
	apiUrl: "http://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/"
};
```

The config file is used in our Model files, in particular our User Model: app/shared/models/User.js:

```
var config = require("../../shared/config");
```

It is used to construct a url via which we can send stringified JSON data via POST:

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

More information on modules can be found [here](http://developer.telerik.com/featured/nativescript-works/).


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

Note our use of the ListView module. In this case, we're not requiring a tns_module from the ui folder, but are rather using the ui widget within the xml code. This is a different way of using these modules. If we wanted to, we could construct a ListView in pure JavaScript code behind the scenes as shown in [this example](http://docs.nativescript.org/ApiReference/ui/list-view/HOW-TO.html). However for our purposes, we can simply use xml to build the ListView and thereby follow the pattern we use in the login and register screens.


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

Notice the use of a slightly different pattern in the code-behind file. In this case, we're going to use a View Model file separately from the code-behind file. We'll get into more detail in the next chapter.
```

### CSS

NativeScript considers CSS a type of tns_module, and you can dig into the code in app/tns_modules/ui/styling. The framework supports a subset of CSS so that you can add styles to your app. We include global styles in app/app.css, where you'll find some styles that format all the textfields, buttons and borders. You can also include individual css files in each view folder, which would be appropriate for styles that are isolated to a certain page.  

We don't have to worry about any particular CSS styling in the login or register screens, but let's turn our attention to the grocery list itself which appears after logging in. It needs a little massaging, so let's add a few CSS styles to app/views/list/list.css:

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

This will give us a lttle more room for our groceries to display nicely.

<img src="images/list-view-2.png"/>

### Other modules

There are several modules that come out of the box with your NativeScript install, including a location service, a file-system helper, timer, camera, and even a color module that helps navigate the ways various colors are handled cross-platform. If you are interested in helping build and distribute more modules for the community, there's a [good guide](http://developer.telerik.com/featured/building-your-own-nativescript-modules-for-npm/) available on how to do this.

