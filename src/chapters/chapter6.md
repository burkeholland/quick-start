## Advanced NativeScript

So far, we've been working with some fairly standard forms and some common use cases for getting a mobile app up and running with NativeScript, including registering and logging into a backend service. Now it's time to dig a little deeper and turn this app into a grocery list app. In this chapter we'll construct a listView that will accept items into a list and save them in the backend database.

### The View Model and Model


To be able to manage data in the grocery list, we need to build a connection between the presentation tier and the database as we did for login. Let's build out these pieces in our grocery list.

**Exercise: Construct the list View Model and Model**

In app/views/list/list.js, add:

```
VM
```

Here, we're creating a new empty grocery array and a groceryList variable to instantiate our GroceryList object that we create in the Model. We're also emptying and refilling our groceryList in the reset function. Let's create the GroceryList object in the Model:

In app/shared/models/GroceryList.js, let's grab any Grocery data that might exist in the backend and push it into the grocery array:

```
Model
```
In the load function above, we are pulling down the list associated to the user's credentials. If you rebuild, run the app, and login as tj.vantoll@gmail.com, you'll find a list of groceries pulled from Backend services in the Groceries data type. It will look something like this:

!()[]

It's great that we can see data already in the database, but we also need to add some items. Let's build that functionality.

**Exercise: Add the ability to create a Grocery item**

Notice at the top of the list is a text area and an 'Add' button. Right now, it doesn't do anything. Let's fix that.

In app/views/list/list.js, add a function to respond to the 'add' tap event that is already in list.xml. We'll put this underneath the navigatedTo function.

```
add routine in VM
```
In this function, we get the 'grocery' item from the input field and add it to the groceryList object. 

Finally, let the manipulation of this new data be handled in the Model by adding the following function under the 'empty' function in /app/shared/models/GroceryList.js:

```
add routine in Model
```

If you build and rerun your app now, you'll find that you can add a grocery item and it will appear immediately in your list.

###Form Validation

You may have noticed that you can get away with submitting a blank list item form. Let's add a bit of form validation to stop a blank email address from being submitted.

>**Todo** validation for list field

Now that we have login, registration, and list routines complete, we need to work on the app's actual functionality as a grocery list management tool.

