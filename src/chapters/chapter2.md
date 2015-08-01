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

> **Tip**: You can learn more about how NativeScript layouts works [in the NativeScript docs](http://docs.nativescript.org/layouts) and [on our blog](https://www.nativescript.org/blog/demystifying-nativescript-layouts).

### CSS

NativeScript uses a [subset of CSS](http://docs.nativescript.org/styling) for adding styles to your app. There are three mechanisms you can use to add CSS properties to UI components: [application-wide CSS](http://docs.nativescript.org/styling#application-wide-css) (`app.css`), [page-specific CSS](http://docs.nativescript.org/styling#page-specific-css), and an [inline `style` attribute](http://docs.nativescript.org/styling#inline-css).

As a best practice, place CSS rules that should apply to all pages in your `app.css`, and CSS rules that apply to a single page in a page-specific CSS file (e.g. `login.css`). Inline styles are great for quick testing—e.g. `<Page style="background-color: green;">`—but should be avoided in general, as the `style` attributes tend to clutter up XML files, especially once you need to apply multiple rules.

Let's start by adding a few application-wide CSS rules.

<h4 class="exercise-start">
    <b>Exercise</b>: Create global styles
</h4>

Paste the following code in your app's `app.css` file:

```
Page {
    background-color: white;
    font-size: 17;
}
TextField {
    margin: 10;
    padding: 10;
    background-color: #FAFAFA;
    border-width: 1;
    border-style: solid;
    border-color: #034793;
}
Button {
    margin: 10;
    border-width: 1;
    border-style: solid;
    border-color: #034793;
}
```

<div class="exercise-end"></div>

If you've done any web development before, the syntax should feel familiar here; you select three UI components (Page, TextField, and Button) by their tag name, and then apply a handful of CSS rules as name/value pairs. Not all web CSS properties are supported, as some aren't possible to replicate in native apps without incurring prohibitive performance penalties. A [full list of the CSS properties that are supported](http://docs.nativescript.org/styling#supported-properties) are listed on the NativeScript docs.

> **Tip**: NativeScript also supports selecting elements by class names and ids. Refer to the docs for [a full list of the supported selectors](http://docs.nativescript.org/styling#supported-selectors).

With these changes in place, if you run your app you'll see some nice styles!

![login 3](images/login-stage3-ios.png)
![login 3](images/login-stage3-android.png)

Because you placed the above CSS rules in `app.css`, they'll apply not only to the login page you're developing now, but also to the register and list pages you'll build later in this guide. Before we end our CSS discussion, let's add one rule that's specific to the login page.

<h4 class="exercise-start">
    <b>Exercise</b>: Create page-specific styles
</h4>

Paste the following code in your app's `views/login/login.css` file:

```
Image {
    margin: 20 0;
}
```

<div class="exercise-end"></div>

Here NativeScript follows [CSS's conventions for specifying multiple values in one rule](https://developer.mozilla.org/en-US/docs/Web/CSS/margin), specifically the first value controls the `margin-top`/`margin-bottom`, and the second value controls the `margin-left/margin-right`. You could alternatively write this CSS as `Image { margin-top: 20; margin-bottom: 20; margin-left: 0; margin-right: 0 }` if you prefer your code to be more explicit.

For now this CSS does nothing as there is no `<Image>` UI component on the login page. Let's change that.

### Images

NativeScript supports three different ways to use images within your apps. The first, and simplest, way is to point at the URL of an image:

```
<Image src="https://www.nativescript.org/images/default-source/landingpages/logo.png" />
```

The second way is to point at an image that lives within your app's `app` folder. For instance if you have an image at `app/images/logo.png`, you can use it with:

```
<Image src="~/images/logo.png" />
```

The third way, and the one Groceries uses, is to use platform-specific image resources. Let's add an image to the login screen and then discuss exactly what's happening.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a logo
</h4>

In `login.xml`, add the `<Image>` below as the first child of the existing `<StackLayout>` tag:

```
<Image src="res://logo" stretch="none" />
```

<div class="exercise-end"></div>

> **TODO**: Move the discussion below to a blog post where we can go into a lot more detail. I feel like this section should only use the simple method of using images within the app, and then link off to a more thorough discussion.

The `res://` syntax tells NativeScript to use a platform-specific resource, in this case an image. You might recall from before that platform-specific resources go in your app's `App_Resources` folder, and if you look there you'll find a few different image files, several of which are named `logo.png`.

Although more complex than putting an image directly in the `app` folder, using platform-specific images gives you more control over image display on difference device dimensions. For instance iOS lets you use provide three different image files for devices with different pixel densities. As such you'll find logos named `logo.png`, `logo@2x.png`, and `logo@3x.png` in your `App_Resources/iOS` folder. For Android you'll find similar image files in `App_Resources/Android/drawable-hdpi` (for "high" dpi, or high dots-per-inch), `App_Resources/Android/drawable-mdpi` (for medium-dpi), and `App_Resources/Android/drawable-ldpi` (for low-dpi).

Once these files are in place the NativeScript framework knows how to pick the correct file; all you have to do is reference the image using `res://` and its base file name—i.e. `res://logo`. With this change in place here's what your login screen should look like on iOS and Android:

![login 4](images/login-stage4-ios.png)
![login 4](images/login-stage4-android.png)

At this point your UI looks good, but the app still doesn't actually do anything. Let's look at how you can use JavaScript to add some functionality.
