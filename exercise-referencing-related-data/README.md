<h1>
  <span class="headline">Cookbook</span>
  <span class="subhead">Exercise</span>
</h1>
## Introduction

In this lab you'll create the Cookbook app, a dynamic recipe organization tool tailored for cooking enthusiasts! For this exercise, you'll be developing a web application that's all about managing recipes and their ingredients. You'll practice referencing related data schemas in MongoDB with two models - `Recipe` and `Ingredient`. Users will have the ability to perform full CRUD operations on recipes, manage their recipe collections, and explore other users' recipes for inspiration.

**MVP: Your primary task is to implement full CRUD operations for the `Recipe` model, using referencing to associate it with ingredients. Full CRUD is not necessary for the `Ingredient` model.**

## User stories and application planning

Use the following user stories to guide the functionality and features of your application:

*   As a User (AAU), I want to sign up for an account and sign in, so that I can access my personal user profile and manage my recipes.
*   AAU, I need a to retrieve all my recipes, to easily manage and review what I have created.
*   AAU, I want to see the full details of each recipe I create.
*   AAU, I want to submit recipe data and receive confirmation that the recipe has been saved.
*   AAU, I need the ability to edit and delete recipes in my collection, allowing me full control over managing my data.
*   AAU, I want to be able to add new ingredients to my recipe manager. This way, I can keep track of the different ingredients I use across various recipes.
*   AAU, I am interested in viewing a list of all other app users, to foster a sense of community and interaction within the app.
*   AAU, I want the option view another user's recipes they have created, to exchange ideas or find inspiration for my own collection.

## Data modelling and ERD

In this lab, your task is to create two primary models: `Recipe` and `Ingredient`. These models represent the core components of your Cookbook app. The `Recipe` model will reference the `Ingredient` model to create a comprehensive list of ingredients for each recipe.

### Create the `Recipe` schema

Create a new file in `models` called `recipe.js`. Create a mongoose schema with the following properties:

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| name | String | true | The name of the recipe. |
| instructions | String | false | The cooking instructions for the recipe. |
| owner | mongoose.Schema.Types.ObjectId | true | A reference to the `User` model. |
| ingredients | \[mongoose.Schema.Types.ObjectId\] | false | An array of references to the `Ingredient` model. |

### Create the `Ingredient` schema

Create a new file in `models` called `ingredient.js`. Create a mongoose schema with the following properties:

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| name | String | true | The name of the ingredient. |

### Referencing Data

In this lab, the `Recipe` model will reference the `Ingredient` model using an array of ObjectIds. Each recipe will be associated with a `User`, thereby creating a relationship between users and their collections of recipes and ingredients.

*   You will be referencing the `Recipe` schema within the `User` schema, establishing a one-to-many relationship
    
*   You will be referencing the `Ingredient` schema within the `Recipe` schema, establishing a many-to-many relationship
    

An ERD (Entity-Relationship Diagram) will help you visualize the relationships between these models:

![Cookbook App ERD](/modular-curriculum-all-courses/men-stack-relating-data-lab-cookbook/assets/erd.png)

In this diagram, you can see how each `Recipe` is connected to a `User` and how `Ingredients` are associated with `Recipes`. This structure forms the backbone of your Cookbook application.

## Define RESTful routes for managing data

Use the following charts as guides when building your RESTful routes.

### Recipe routes

| Action | Route | HTTP Verb |
| --- | --- | --- |
| Index | `/recipes` | GET |
| Create | `/recipes` | POST |
| Show | `/recipes/:recipeId` | GET |
| Update | `/recipes/:recipeId` | PUT |
| Delete | `/recipes/:recipeId` | DELETE |

### Ingredient routes

| Action | Route | HTTP Verb |
| --- | --- | --- |
| Index | `/ingredients` | GET |
| Create | `/ingredients` | POST |
| Show | `/ingredients/:ingredientId` | GET |
| Update | `/ingredients/:ingredientId` | PUT |
| Delete | `/ingredients/:ingredientId` | DELETE |

> Note: you may **not** need **_all_** of these routes in your application

## Structure and Setup

### 1\. Build the Controllers

*   Create files for the `recipes` and `ingredients` controllers in the controllers directory.
*   Require all essential modules at the top of each controller file.
*   Export your controllers for use in routers.

```javascript
// controllers/recipes.js

const User = require('../models/user.js');
const Recipe = require('../models/recipe.js');

// controller logic will go here - will be built later on in the lab

module.exports = {};
```

Repeat this process for the `ingredients` controller.

### 2\. Build the Routers

*   Create files for the `recipes` and `ingredients` routers in the routers directory.
*   Require all essential modules and controllers at the top of each controller file.
*   Export your routers for use in `server.js`.

```javascript
// routers/recipes.js

const express = require("express")
const router = express.Router()

// router logic will go here - will be built later on in the lab

module.exports = router;
```

Repeat this process for the `ingredients` router.

### 3\. Use the routers in server

*   Import the `recipes` and `ingredients` routers in `server.js`.

```js
// server.js

const authRouter = require('./routers/auth.js');
const recipesRouter = require('./routers/recipes.js');
const ingredientsRouter = require('./routers/ingredients.js');
```

```js
// server.js

// below middleware
app.use('/auth', authRouter);
app.use('/recipes', recipesRouter);
app.use('/ingredients', ingredientsRouter);
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
app.use('/recipes', recipesController);
app.use('/ingredients', ingredientsController);
```

You are now ready to begin building your protected routes.

## Build the `recipe` routes

With the main structure of the application complete, it's time to start building out the `recipe` pages guided by user stories.

## Build the `get` functionality

Implement the following user story:

> AAU, I need a to retrieve all my recipes, to easily manage and review what I have created.

### 1\. Conceptualise the `get` route

This route will:

*   `GET` from `/recipes`

### 2\. Define the `get` route

*   Define the get route in the router

### 3\. Build the `get` controller

This controller will return the \_id and name of all the recipes of the current user.

*   Look up the current user from the `JWT (req.decoded)`
*   Return all recipes for the current user

## Build the `create` functionality

Implement the following user story:

> AAU, I want to submit recipe data and receive confirmation that the recipe has been saved.

### 1\. Conceptualize the `create` route

This route will:

*   `POST` to `/recipes`

### 2\. Define the `create` route

*   Define the create route in the router

### 3\. Build the `create` controller

This controller will create a new `recipes`.

*   Look up the current user from the `JWT (req.decoded)`
*   Add recipe data to the current user.
*   Save changes.
*   If any errors, inform user of the error.

## Build the `get` functionality

Implement the following user story:

> AAU, I want to see the full details of each recipe I create.

### 1\. Conceptualize the `get` route

This route will:

*   `GET` from `/recipes/:recipeId`

### 2\. Define the `get` route

*   Define the get route in the router

### 3\. Build the `get` controller

This controller will return all the recipe details of the required recipe.

*   Look up the current user from the `JWT`
*   Return all recipe details from the `recepies` of the required recipe of the current user

## Build `delete` functionality

Implement the following user story:

> AAU, I need the ability to edit and delete recipes in my collection, allowing me full control over managing my data.

### 1\. Conceptualize the `delete` route

This route will:

*   `DELETE` to `/recipes/:recipeId`

### 2\. Define the `delete` route

*   Define the `delete` route in the router

### 3\. Build the `delete` functionality

This route will delete the recipe from the current user.

*   Look up the user from the `JWT (req.decoded)`
*   Use the `.deleteOne()` method to delete a recipe using the id supplied from `req.params` and the `username` from the JWT
*   Save changes to the `user` and return a successful message
*   If any errors, inform user of it.

## Build the `update` functionality

### 1\. Conceptualize the `update` route

This route will:

*   `POST` to `/recipe/:recipeId`

### 2\. Define the `update` route

*   Define the update route in the router

### 3\. Build the `update` functionality

This route will update a specific food item from the user's pantry:

*   Find the user from the `JWT (req.decoded)`
*   Find the recipe from the id supplied by `req.params`
*   Use the `.set()` method, updating the recipe to reflect the new form data on `req.body`
*   Save the recipe and return a successful message
*   If any errors, inform the user about it.

Now that you have full crud functionality, test each route end to end and check for errors before moving on.

Now that you have full crud functionality for recipes, test each route end to end and check for errors before moving on.

  

## Connect `Ingredients` to `Recipes`

In this part of the lab, you'll integrate an `Ingredient` model into the `Recipe` creation and management process.

**We've intentionally provided less guidance in this section to encourage creative problem-solving.**

Implement the following user stories:

> AAU, I want to be able to add new ingredients to my recipe manager. This way, I can keep track of the different ingredients I use across various recipes.

After you've achieved full CRUD functionality for recipes, your next step will be to integrate ingredients. Start by creating a simple list of all ingredients `GET /ingredients` and a route to add new ones `POST /ingredients`. Then, focus on allowing users to add these ingredients to their recipes. How and where you choose to make those relationships is up to you.

### Ingredient routes and logic

**Note:** It might not be necessary to develop full RESTful routes for `Ingredient`.

Instead, consider:

*   You might only need an index `GET /ingredients` and a create route `POST /ingredients` for ingredients. How would you structure these to efficiently manage ingredient data?
    

### Simplifying recipe and ingredient integration

*   Consider that you may only need one or two additional routes in your `recipes` router to relate ingredients to recipes. For instance, a single route could handle adding ingredients directly from the recipe show or edit page.
    
*   Remember not to over complicate your routing. Focus on what's necessary for efficient functionality.
    

### Handling ingredient duplication

*   When adding ingredients to recipes, think about how you might unintentionally allow for duplicate ingredients. What are some strategies to prevent this, either in the view or within your route logic?
    

As you progress through this section of the lab, keep in mind the simplicity and functionality of your application. While it might be tempting to create a comprehensive system for managing ingredients, sometimes less is more. Focus on the routes that are essential for a smooth user experience.

### Congrats! You have reached MVP! 🎉

  

## Add a Community routes (Level Up)

There are two final `user` routes to reach your final application. Building a community route for users to find each other and gain inspiration from the recipes of others.

Implement the following user story:

> AAU, I am interested in viewing a list of all other app users, to foster a sense of community and interaction within the app.

To meet this requirement you will need a new `users` controller and `users` router.

*   Create a simple controller in `/users` to get all users and a route to link to the controller.

## View other users' `recipes`

Implement the following user story:

> AAU, I want the option view another user's recipes they have created, to exchange ideas or find inspiration for my own collection.

The users should be able to use the id of other users and see their recipes. This requires one final route.

*   Create a controller in `/users` to get all users and a route to link to the controller.
*   Create a another controller in `/users` to get all the recepies of the requested user and a route to link to the controller.
*   Ensure that if it not the owner's recipe they can only view.

Test your new community page.