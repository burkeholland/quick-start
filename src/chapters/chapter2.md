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

- **app**: This folder contains all the development resources you need to build your app.
- **package.json**: This file contains configuration about your app, such as your app id, the version of NativeScript you're using, and also which npm modules your app uses. We'll take a closer look at how to use this file when we talk about using npm modules in chapter 5.
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

- **App_Resources**: This folder contains platform-specific resources such as icons, splash screens, and configuration files. The NativeScript CLI takes care of injecting these resources into the appropriate places in the ```platforms``` folder when you execute ```tns run```.
- **shared**: This folder contains any files you need to share across views in your app. In the Groceries app you find a few model objects and a ```config.js``` file used to share configuration variables like API keys.
- **tns_modules**: This folder contains the NativeScript-provided modules you'll use to build your app. Each module contains platform-specific code (camera, http, file system, etc), exposed through a platform-agnostic API (e.g. ```http.getJSON()```). We'll look at some examples momentarily.
- **views**: This folder contains the code to build your app's views—each of which will have a subfolder in `views`. Each view is made up of an XML file, a JavaScript file, and an optional CSS file. The groceries app contains three folders for its three views.
- **app.css**: Contains global styles for your app. We'll dig into app styling in section 2.3.
- **app.js**: Sets up your application's starting module and initializes the app.

Let's start with `app/app.js`, as it's the starting point for NativeScript apps. It contains the three lines below: 

```
var application = require("application");
application.mainModule = "./views/login/login";
application.start();
```

Here, you're requiring, or importing, the [NativeScript application module](http://docs.nativescript.org/ApiReference/application/HOW-TO). Then, you set the main (or start) screen of your app to be the login screen, which lives in your app's `views/login` folder.

> **Tip**: JavaScript modules in NativeScript follow the [CommonJS specification](http://wiki.commonjs.org/wiki/CommonJS). This means you can use the [`require()` method](http://wiki.commonjs.org/wiki/Modules/1.1#Module_Context) to import modules, as is done above, as well as the `export` keyword, which we'll look at later in this chapter. These are the same constructs Node.js uses for JavaScript modules, so if you already know how to use Node.js modules, you already know how to use NativeScript modules!

Now that your app is ready for development, let's add some UI components to make your login screen show more than just some basic text.

### Adding UI Components

Let's dig into the files used to create your app's UI, which reside in the `app/views` folder. Each folder contains one page of your app: `list`, `login`, and `register` are there already. If you look at `app/views/login`, you'll see three files: `login.css`, `login.js`, and `login.xml`. Open up the `login.xml` file. You should see the following code:

```
<Page>
    <Label text="hello world" />
</Page>
```

This page currently contains two UI components: a `<Page>` and a `<Label>`. To make this page look more like a login page, let's add a few additional components, namely two `<TextField>`s and two `<Button>`s.

<h4 class="exercise-start">
    <b>Exercise</b>: Add UI components to <code>login.xml</code>
</h4>

Replace the existing `<Label>` with the following code:

```
<TextField hint="Email Address" keyboardType="email" />
<TextField secure="true" hint="Password" />

<Button text="Sign in" />
<Button text="Sign up for Groceries" />
```

<div class="exercise-end"></div>

NativeScript UI components use attributes to configure their behavior and appearance. The code you just added uses the following attributes:

- `<TextField>`
    - `hint`: Used to specify placeholder text into the TextField to show the user what to type
    - `secure`: Boolean to determine whether the TextField's text should be masked, which is commonly done on password fields
    - `keyboardType`: The type of keyboard to present to the user for input. In this case `keyboardType="input"` ensures the keyboard in a way optimized for entering email addresses
- `<Button>`
    - `text`: Controls the text displayed within the button

Since UI components are visual, you probably want to see what your app looks like. But if you try running the app you won't see too much:

![login 1](images/login-stage1-ios.png)
![login 1](images/login-stage1-android.png)

This app looks off because you need to tell NativeScript how to layout the UI components you place in your page. Let's look at how to do that next.

> **Tip**: You can learn more about the UI components, including a full list of the components and attributes available in [the NativeScript docs](http://docs.nativescript.org/ui-with-xml).

### Layouts 

NativeScript provides several different layout containers that allow you to place UI components precisely where you want them to appear. 

- The [Absolute Layout](https://docs.nativescript.org/ApiReference/ui/layouts/absolute-layout/HOW-TO.html) lets you position elements using explicit x and y coordinates. This is useful when you need to place elements in exact locations, for instance showing an activity indicator widget in the top-left corner of your app.
- The [Dock Layout](https://docs.nativescript.org/ApiReference/ui/layouts/dock-layout/HOW-TO.html) is Useful for placing UI elements at the outer edges of your app. For example, a container docked at the bottom of the screen would be a good container for an ad.
- The [Grid Layout](https://docs.nativescript.org/ApiReference/ui/layouts/grid-layout/HOW-TO.html) lets you divide your interface into a series of rows and columns, much like a `<table>` in HTML markup.
- The [Stack Layout](https://docs.nativescript.org/ApiReference/ui/layouts/stack-layout/HOW-TO.html) lets you stack children UI components either vertically or horizontally.
- The [Wrap Layout](https://docs.nativescript.org/ApiReference/ui/layouts/wrap-layout/HOW-TO.html) allows child UI components to flow from one row or column to the next when space is filled.

In the case of your login screen all you need is a simple `<StackLayout>` to stack up the elements on the screen. In later sections, you'll use some of the more advanced layouts.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a stack layout to the login screen</code>
</h4>

In your `login.xml`, add the `<StackLayout>` component below directly within the `<Page>` component. Your `login.xml` should look something like this:

```
<Page>
    <StackLayout orientation="vertical" horizontalAlignment="center">
        ... (the two buttons and text fields)
    </StackLayout>
</Page>
```

<div class="exercise-end"></div>

The stack layout is a UI component, and as such, it that has attributes just like the `<TextField>` and `<Button>` elements you used in the previous section. Here, the `orientation="vertical"` attribute tells the stack layout to arrange its child components vertically, and the `horizontalAlignment="center"` attribute tells the stack layout to align the child components in the center of the screen.

If you run your app you'll see that that the elements now all appear, and are stacked up as expected:

![login 2](images/login-stage2-ios.png)
![login 2](images/login-stage2-android.png)

However, although the elements stack up as expected, the UI components could use some spacing, and a bit of color to make the app look a bit nicer. To do that let's look at another NativeScript feature: CSS.

> **Tip**: You can learn more about how NativeScript layouts works [in the NativeScript docs](http://docs.nativescript.org/layouts) and [on our blog](http://developer.telerik.com/featured/demystifying-nativescript-layouts/).

### CSS

NativeScript supports a [subset of CSS](http://docs.nativescript.org/styling) so that you can add styles to your app. You can include global styles in a css file in the root of your app. You can also include individual css files in each view folder, which would be appropriate for styles that are isolated to a certain page.  

<h4 class="exercise-start">
    <b>Exercise</b>: Create global styles
</h4>

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

<div class="exercise-end"></div>

Now if you run the app, you'll see some nice styles!

![login 3](images/login-stage3-ios.png)
![login 3](images/login-stage3-android.png)

### Images

It would be nice to have a logo available to match the imagery that is in the icon and on the splash screen. 

<h4 class="exercise-start">
    <b>Exercise</b>: Add a logo
</h4>

Add a logo at the top of the login.xml, under the first StackLayout tag:

```
<Image src="res://logo" stretch="none"/>
```

<div class="exercise-end"></div>

Images are handled differently across platforms. They need to be saved in three different sizes so that they will look sharp on different screen sizes. For iOS, you would create an image.png, image@2x.png, and image@3x.png with consistently larger sizes. For Android, your images need to avoid any special symbols like '@', so all you need to do is save the file ```logo.png``` in three different sizes. 

All of these scaled resources can go into ```app/App_Resources```. 

For iOS, you would place the three scaled images into the ```app/App_Resources/iOS``` folder. When you build your app, you'll see the files saved in ```platforms/ios/Groceries/Resources/icons/``` where they are referenced by the native code and appear scaled on the appropriate screen.

For Android, you would place a scaled image in the appropriate ```app/App_Resources/Android``` drawable subfolder. The largest goes into drawable-hdpi (for "high" dpi, or high dots-per-inch). The next largest goes into drawable-mdpi (medium-dpi), and the smallest goes into drawable-ldpi (low-dpi). When the app is compiled, you'll find the files in the appropriate ```platforms/android/res/``` subfolder. 

The native code will pick the right file to display; all you have to do is reference the image src to be ```res://myImage```. 

Go ahead and build your app for iOS and Android. Check out how the images find their way into the right place.

![login 4](images/login-stage4-ios.png)
![login 4](images/login-stage4-android.png)

Now that your UI looks good, we can move on to making this app a little more functional. In the next chapter, we'll build out the view model files.

