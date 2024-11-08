# ![MEN Stack Relating Data Lab - Cookbook - Exercise](./assets/hero.png)

## Introduction

In this lab you'll create the Cookbook app, a dynamic pantry manager for cooking enthusiasts! For this exercise, you'll be practicing embedding a new data schema within a user model. Users will have the power to perform full CRUD operations to manage their pantry items with ease. They'll also have the opportunity to explore other users' pantries, drawing inspiration and sharing their love for cooking.

**MVP: Your task is to perform full CRUD on items in a User's `pantry` - an embedded data schema on the `user` model.**

## User stories and application planning

Use the following user stories to guide the functionality and features of your application:

*   As a User (AAU), I want to sign up for an account and sign in, so that I can access my personal user profile and manage my pantry.
*   AAU, I need a to view all items in my pantry, to easily manage and review what I have stored.
*   AAU, I want to add new items to my pantry and after submission to receive confirmation that the item has been saved in my pantry.
*   AAU, I need the ability to edit and delete items in my pantry, allowing me full control over managing my stored items.
*   AAU, I am interested in viewing a list of all other app users, to foster a sense of community and interaction within the app.
*   AAU, I want the option to view another user's items in their pantry, to exchange ideas or find inspiration for my own pantry.

## Data modelling and ERD

Your task is to create a `Pantry`, an embedded array within the `User` model. This array represents the user's collection of food items. Because the pantry holds foods, the name of our schema will be `foodSchema`.

### Create the `Food` schema

Each user's pantry will contain an array of `foods`, each defined by a simple schema:

| Property | Type | Required |
| --- | --- | --- |
| name | String | true |

Begin by defining `foodSchema` in the `user` model file using the `new mongoose.Schema` method. Include the necessary properties for food items.

```js
// user.js

const foodSchema = new mongoose.Schema({
  // YOU DO: Define properties of food schema
});
```

### Embed `foodSchema` in `userSchema`

Next, modify `userSchema` to include the `foodSchema`. Do this by adding a property named `pantry`. This property will utilize the `foodSchema` to represent a list of food items associated with a user.

```js
const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
  },
  password: {
    type: String,
    required: true,
  },
  pantry: // YOU DO: embed foodSchema here
});
```

Take at look at this ERD (Entity-Relationship Diagram) to help you visualize the relationships between these entities, `User` and `foodSchema`:

![Cookbook App ERD](/modular-curriculum-all-courses/men-stack-relating-data-lab-cookbook/assets/erd.png)

> For this exercise, you can ignore the schemas for recipe and ingredient. Additional sections of this lab explore relating data using referencing.

## RESTful routes for managing data

Reference this chart when building your RESTful routes in your controller.

| Action | Route | HTTP Verb |
| --- | --- | --- |
| Index | '/users/foods' | GET |
| Create | '/users/foods' | POST |
| Show | '/users/foods/:itemId' | GET |
| Update | '/users/foods/:itemId' | PUT |
| Delete | '/users/foods/:itemId' | DELETE |

_Because this data is embedded in the user model, our routes are structured to reflect that relationship._

## Structure and Setup

### 1\. Build the controller

*   Create a file called `foods.js` in the controllers directory.
*   Require all essential modules at the top of `foods.js`
*   Export your router for use in router `foods.js`

```js
// controllers/foods.js

const User = require('../models/user.js');

// controller logic will go here - will be built later on in the lab

module.exports = {};
```

### 2\. Use the controllers in the router

*   Create a file called `foods.js` in the routers directory.
*   Import the foods controller
*   Create the routes and link to the controllers
*   Export your router for use in server.js

```js
// routers/foods.js

const express = require("express")
const router = express.Router()

// routers will go here - will be built later on in the lab
        
module.exports = router;
        
```

### 3\. Use the routers in server

*   Import the foods router in `server.js`.
*   Use middleware to direct incoming requests to `/users/:userId` to the foods controller.

```js
// server.js

const authRouter = require('./routers/auth.js');
const foodsRouter = require('./routers/foods.js');
```

```js
// server.js

app.use('/auth', authRouter);
app.use('/users/foods',foodsRouter);
```

## Add middleware

Now you'll need to a middleware functions to your application:

*   one that restricts access to logged-in users only

Create a `middleware` folder in your project root. Inside this folder:

Create a file named `is-signed-in.js`. This function should:

*   include logic that checks if the JWT is valid.
*   If it does, allows the request to continue on the normal chain by invoking `next()`
*   Else return an error message of not authorised.

```js
// middleware/is-signed-in.js

const isSignedIn = (req, res, next) => {
  // YOU DO: complete the codes

  try {
   const decoded = jwt.verify(token, ACCESS_SECRET);
   next()
  } catch (error) {
    return res.status(401).json({ error: "not authorised" })
  }
};

module.exports = isSignedIn;
```

Import and include the middleware function above all of the routes and controllers in `server.js.`.

```js
// server.js

const isSignedIn = require('./middleware/is-signed-in.js');
```

For this application, users must be signed in to view any of the routes associated with their pantry. Therefore, isSignedIn should come above the foods controller, but not before auth.

```js
app.use('/auth', authController);
app.use(isSignedIn)
app.use('/users/:userId/foods',foodsController);
```

You are now ready to begin building your protected routes.

## Build the `create` functionality

Implement the following user story:

> AAU, I want to add new items to my pantry and after submission to receive confirmation that the item has been saved in my pantry.

### 1\. Conceptualize the `create` route

This route will:

*   `POST` to `/users/foods`

### 2\. Define the `create` route

*   Define the create route in the router

### 3\. Build the `create` controller

This controller will create new `foods` in the embedded pantry array on the `user` model.

*   Look up the current user from the `JWT (req.decoded)`
*   Push `req.body` (the new input data object) to the `pantry` array of the current user.
*   Save changes to the `user`.
*   If any errors, inform user of the error.

## Build the `get` functionality

Implement the following user story:

> AAU, I need a to view all items in my pantry, to easily manage and review what I have stored.

### 1\. Conceptualize the `get` route

This route will:

*   `GET` from `/users/foods`

### 2\. Define the `get` route

*   Define the get route in the router

### 3\. Build the `get` controller

This controller will return all the pantry items of the current user.

*   Look up the current user from the `JWT (req.decoded)`
*   Get all items from the `pantry` of the current user
*   Return all items

## Build `delete` functionality

Implement the following user story:

> AAU, I need the ability to edit and delete items in my pantry, allowing me full control over managing my stored items.

### 1\. Conceptualize the `delete` route

This route will:

*   `DELETE` to `/users/foods/:itemId`

### 2\. Define the `delete` route

*   Define the `delete` route in the router

### 3\. Build the `delete` functionality

This route will delete the food item from the user's pantry.

*   Look up the user from the `JWT (req.decoded)`
*   Use the `.deleteOne()` method to delete a food using the id supplied from `req.params` and the `username` from the `JWT`.
*   Save changes to the `user` and return a successful message
*   If any errors, inform user of it.

## Build the `update` functionality

### 1\. Conceptualize the `update` route

This route will:

*   `POST` to `/users/foods/:itemId`

### 2\. Define the `update` route

*   Define the update route in the router

### 3\. Build the `update` functionality

This route will update a specific food item from the user's pantry:

*   Find the user from the `JWT (req.decoded)`
*   Find the current food from the id supplied by `req.params`
*   Use the `.set()` method, updating the current food to reflect the new form data on `req.body`
*   Save the current `user` and return a successful message
*   If any errors, inform the user about it.

Now that you have full crud functionality, test each route end to end and check for errors before moving on.

## Add a Community routes

There are two final `user` views to reach your final application. Getting a list of all users for users to find each other and gain inspiration from the pantry items of others.

Implement the following user story:

> AAU, I am interested in viewing a list of all other users, to foster a sense of community and interaction.

To meet this requirement you will need a new `users` controller.

*   Create a `users` controller to get all users.
*   Create a `users` route and connect to the controller
*   Connect your new controller in `server.js`

## View other users' pantries

Implement the following user story:

> AAU, I want the option to view another user's items in their pantry, to exchange ideas or find inspiration for my own pantry.

Users should be able to view other users' pantry. This requires one final route.

*   Create a `get` route `/users/:userId`
*   Build the controller to return another user's using the :userId parameter.

Test your routes.

### Congrats! You have reached MVP! 🎉