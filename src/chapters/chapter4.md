## NativeScript modules

In the previous chapter, we already saw how NativeScript leverages the concept of 'modules' to include bits of code that are kept in the tns_modules folder. Using 'require', you can include these snippets ad hoc in your code when you need to use them, similar to the way we use npm to import node libraries. Let's take a closer look at these modules and what they can do for us.

>If you dig a bit into the tns_modules folder and find the http folder, you can see how a tns module is constructed. It includes:
- a package.json that sets the name of the module and includes the base http.js file
- a file containing android native code (http-request.android.js) 
- a file containing ios native code (http-request.ios.js)
- a generic file (http.js) that abstract the platform-specific code above into a platform-agnostic format readable by the NativeScript runtime.

>More information on modules can be found [here](http://developer.telerik.com/featured/nativescript-works/).

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
```
Notice the use of a slightly different pattern in the code-behind file. In this case, we're going to use a View Model file separately from the code-behind file. We'll get into more detail in the next chapter.

### Other modules

There are several modules that come out of the box with your NativeScript install, including a location service, a file-system helper, timer, camera, and even a color module that helps navigate the ways various colors are handled cross-platform. If you are interested in helping build and distribute more modules for the community, there's a [good guide](http://developer.telerik.com/featured/building-your-own-nativescript-modules-for-npm/) available on how to do this.

