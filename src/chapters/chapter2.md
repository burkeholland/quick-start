## Building the UI

Before you start coding the Groceries app it's important to understand a NativeScript app's folder structure. It'll help you understand where to place new files, as well as a bit of what's going with NativeScript on under the hood.

Go ahead and open the Groceries app's files up in your text editor of choice and let's dig in.

### Directory structure

To keep things simple, let's start by looking at the outer structure of the Groceries app:

```
.
└── Groceries
    ├── app
    │   └── ...
    ├── package.json
    └── platforms
        ├── android
        └── ios
```

Here's what these various files and folders do:

- **app**: The `app` folder contains all the development resources you need to build your app.
- **package.json**: This contains configuration about your app, such as your app id, the version of NativeScript you're using, and also which npm modules your app uses. We'll take a closer look at how to use this file when we talk about using npm modules in chapter 5.
- **platforms**: This folder contains the platform-specific code NativeScript needs to build native iOS and Android apps. For instance in the `android` folder you'll find things like your project's `AndroidManifest.xml` and .apk executable files. Similarly the `ios` folder contains the Groceries' Xcode project and the .ipa executables.

The NativeScript CLI manages the `platforms` folder for you as you develop and run your app; therefore it's a best practice to treat the `platforms` folder as generated code. The Groceries app includes the `platforms` folder in its `.gitignore` to exclude its files from source control.

Next, let's dig into the ```app``` folder, as that's where you'll be spending the majority of your time.

```
.
└── Groceries
    ├── app
    │   ├── App_Resources
    │   │   ├── Android
    │   │   └── iOS
    │   ├── shared
    │   │   └── ...
    │   ├── tns_modules
    │   │   └── ...
    │   ├── views
    │   │   └── login
    │   │       ├── login.js
    │   │       └── login.xml
    │   ├── app.css
    │   ├── app.js
    │   └── ...
    └── ...
```

Here's what these various files and folders do:

- **App_Resources**: Contains platform-specific resources such as icons, splash screens, and configuration files. The NativeScript CLI takes care of injecting these resources into the appropriate places in the ```platforms``` folder when you execute ```tns run```.
- **shared**: This folder contains any files you need to share across views in your app. In the Groceries app you find a few model objects and a ```config.js``` file used to share configuration variables like API keys.
- **tns_modules**: This folder contains the NativeScript-provided modules you'll use to build your app. Each module contains platform-specific code (camera, http, file system, etc), exposed through a platform-agnostic API (e.g. ```http.getJSON()```). We'll look at some examples momentarily.
- **views**: Each of your app's views will have a subfolder in ```views```. Each view is made up of an XML file, a JavaScript file, and an optional CSS file. The groceries app contains three folders for its three views.
- **app.css**: Contains global styles for your app. We'll dig into app styling in section 2.3.
- **app.js**: Sets up your application's starting module and initializes the app.

Let's start with `app/app.js`, as it's the starting point for NativeScript apps. It contains the three lines below: 

```
var application = require("application");
application.mainModule = "./views/login/login";
application.start();
```

Here, you're requiring, or importing, the NativeScript application module. Then, you set the main screen of your app to be the `login` screen, which we'll dig into momentarily.

>NativeScript supports JavaScript modules and their implementation follows the [CommonJS specification](http://wiki.commonjs.org/wiki/CommonJS). Using the '[require](http://wiki.commonjs.org/wiki/Modules/1.1#Module_Context)' function at the top of this file allows us to identify a module to be imported, and it returns the exported API of this module. Similarly, we see the variable ```exports```, which is an object that the module may add its API to as it executes.

Now that your app is ready for development, you can turn your attention to its UI. Let's add some UI components to make your login screen show more than just an image.

### Adding UI Components

First let's take a look at the UI components. Let's dig into the files where you create the UI, which reside in the ```app/views``` folder. Each folder contains one page of your app: list, login, and register are there already. If you look at ```app/views/login```, you'll see three files. Open up the ```login.xml``` file. 

NativeScript allows you to construct a UI either using xml or JavaScript. Often, it's a bit easier to write your presentation layer in xml. Build out the login screen by adding some UI components, namely two textfields, and a button.

**Exercise: Add UI components to login.xml**

Between the <Page> tags, add the following code:

```		
	<TextField hint="Email Address" />
		
	<TextField secure="true" hint="Password" />
		
	<Button text="Sign in" />
		
	<Button text="Sign up for Groceries" />
		
```
You've just added four items to your screen:
- The TextField has the attributes you'd expect such as hints, which adds the specify hint text into the TextField to show the user what to type, and the parameter 'secure' to ensure that a password isn't exposed. 
- The Button component has its text, "Sign in" or "Sign Up for Groceries", specified. 

If you run your app at this point, however, you won't see too much:

![login 1](images/login-stage1-ios.png)
![login 1](images/login-stage1-android.png)

You need to specify a layout for this page to ensure that the children of the layout are properly styled.

>*Tip*: Learn more about the UI components available in your app [here](http://docs.nativescript.org/ui-with-xml).

### Layouts 

NativeScript provides several different layout containers that allow you to place UI components precisely where you want them to appear. 

- [Absolute Layout](https://docs.nativescript.org/ApiReference/ui/layouts/absolute-layout/HOW-TO.html): Position elements by a child's x and y coordinates. You might use absolute layouts to show an activity indicator widget in the top left corner of your app.
- [Dock Layout](https://docs.nativescript.org/ApiReference/ui/layouts/dock-layout/HOW-TO.html): Dock layouts are useful if you need to place elements in very specific areas of your app. For example, a container docked at the bottom of the screen would be a good container for an ad.
- [Grid Layout](https://docs.nativescript.org/ApiReference/ui/layouts/grid-layout/HOW-TO.html): Look at grid layouts as the <tr> and <td> tags of NativeScript. Create a grid and add children to it with rowSpans and colSpans, similar to HTML markup for tables.
- [Stack Layout](https://docs.nativescript.org/ApiReference/ui/layouts/stack-layout/HOW-TO.html): Stack children of this layout either vertically or horizontally.
- [Wrap Layout](https://docs.nativescript.org/ApiReference/ui/layouts/wrap-layout/HOW-TO.html): Children of this layout will flow from one row or column to the next when space is filled.

**Exercise: Add a stacked layout to the login screen**

In login.xml, add StackLayout tags under the ```<Page>``` tags. This will allow you to arrange your content vertically and align it to the center.

```
<StackLayout orientation="vertical" horizontalAlignment="center">
...
</StackLayout>
```

>*Tip*: Learn more about creating NativeScript layouts [here](http://docs.nativescript.org/layouts) and [here](http://developer.telerik.com/featured/demystifying-nativescript-layouts/).

Even if you specify a layout for your app, the page still doesn't look right. If run your app at this point, the fields are all jammed at the top:

![login 2](images/login-stage2-ios.png)
![login 2](images/login-stage2-android.png)


It needs some CSS styling to look better.

### CSS

NativeScript supports a [subset of CSS](http://docs.nativescript.org/styling) so that you can add styles to your app. You can include global styles in a css file in the root of your app. You can also include individual css files in each view folder, which would be appropriate for styles that are isolated to a certain page.  

**Exercise: Create global styles**

```
Page {
	background-color: white;
	font-size: 17;
}
Image {
	margin: 20 0;
}
Label {
	/* left, top, right, bottom */
	margin: 20, 0, 0, 0;
	width: 300;
}
TextField {
	margin: 10;
	background-color: #FAFAFA;
	padding: 10;
}
Button {
	margin: 10;
}
```

Now if you run the app, you'll see some nice styles!

![login 3](images/login-stage3-ios.png)
![login 3](images/login-stage3-android.png)

### Images

It would be nice to have a logo available to match the imagery that is in the icon and on the splash screen. 

**Exercise: Add a logo**

Add a logo at the top of the login.xml, under the first StackLayout tag:

```
<Image src="res://logo" stretch="none"/>
```

Images are handled differently across platforms. They need to be saved in three different sizes so that they will look sharp on different screen sizes. For iOS, you would create an image.png, image@2x.png, and image@3x.png with consistently larger sizes. For Android, your images need to avoid any special symbols like '@', so all you need to do is save the file ```logo.png``` in three different sizes. 

All of these scaled resources can go into ```app/App_Resources```. 

For iOS, you would place the three scaled images into the ```app/App_Resources/iOS``` folder. When you build your app, you'll see the files saved in ```platforms/ios/Groceries/Resources/icons/``` where they are referenced by the native code and appear scaled on the appropriate screen.

For Android, you would place a scaled image in the appropriate ```app/App_Resources/Android``` drawable subfolder. The largest goes into drawable-hdpi (for "high" dpi, or high dots-per-inch). The next largest goes into drawable-mdpi (medium-dpi), and the smallest goes into drawable-ldpi (low-dpi). When the app is compiled, you'll find the files in the appropriate ```platforms/android/res/``` subfolder. 

The native code will pick the right file to display; all you have to do is reference the image src to be ```res://myImage```. 

Go ahead and build your app for iOS and Android. Check out how the images find their way into the right place.

![login 4](images/login-stage4-ios.png)
![login 4](images/login-stage4-android.png)


### Code-behind files

Although you can now see your login screen as a nicely-styled entity with UI widgets and an image, it's not yet wired up to send data to the backend. Let's fix that.

In ```app/views/login```, you'll find ```login.js```. This is called a 'code-behind' file because it supports the xml markup that constructs the presentation tier. 

**Exercise: Construct the login code-behind file.**

First, add a 'loaded' attribute to the Page element at the top of login.xml:

```
<Page loaded="load">
```

Now you can build up a load function in ```app/views/login/login.js```:

```
exports.load = function() {
	console.log("hello")
};
```
If you run the app, you can see how, when the login screen loads, you can view the word 'hello' in the app console.

With this simple example, we can see how the xml file can append attributes to the markup that point to various functions in the code-behind files. One such attribute is 'load', and another is 'tap'.

**Exercise: Add tap attributes to the login buttons and add their functions**

You can add a 'tap' attribute that will fire when a button is tapped or touched. 

In ```app/views/login/login.xml```, edit the markup for both buttons at the bottom of the screen:

```
<Button text="Sign in" tap="signIn" />

<Button text="Sign up for Groceries" tap="register" />
```
Then, in ```apps/views/login/login.js```, create the 'signIn' and 'register' functions:

exports.signIn = function() {
	alert("Signing in");
};

exports.register = function() {
	alert("Registering");
};

At this point, if you run your app and tap either of the buttons in your simulator, you will see the appropriate alerts pop up. 

![login 5](images/login-stage5-ios.png)
![login 5](images/login-stage5-android.png)

In the next chapter, we'll build out the code-behind files more so that they can do something more useful!

