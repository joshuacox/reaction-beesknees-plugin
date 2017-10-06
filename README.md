# Creating a Reaction plugin

## In this tutorial

This tutorial covers many different parts of an application you can change with a plugin:

1. [Custom layouts]("/developer/tutorial/plugin-layouts-3.md"): Create a custom layout
2. [Custom templates]("/developer/tutorial/plugin-customizing-templates-4.md"): Customize the layout template
3. [Fixtures]("/developer/tutorial/plugin-fixtures-5.md"): Add fixture data
4. [Routes]("/developer/tutorial/plugin-routes-6.md"): Add a new route
5. [Workflows]("/developer/tutorial/plugin-workflow-7.md"): Customize workflow
6. [Schemas]("/developer/tutorial/plugin-schemas-8.md"): Customize collection schema
7. [Internationalization]("/developer/tutorial/plugin-i18n-9.md"): Add i18n copy

As you start building your own plugins, consider separating customizations into their own individual plugins.

# Page 1

## In this step, you'll learn

1. How to create a blank Reaction plugin
2. How to set up git submodules

## 1. Create a blank plugin

First, we'll start a blank plugin and set up git submodule version control for it.

1. Start with a fresh checkout of the latest version of Reaction:

```sh
reaction init my-store
cd my-store
```

2. Create a directory in `imports/plugins/custom` with the name of your plugin:

```sh
mkdir imports/plugins/custom/reaction-beesknees-plugin/
```
Reaction by default adds all files added to the `/custom/` to `.gitignore`. Instead of tracking plugin changes in the main Reaction repository, use Git submodules to keep the plugin repository separated from Reaction.

## 2. Set up git submodule

1. Create a [new GitHub repository](https://github.com/new). Choose a repository name and initialize the repository with a README.

2. Get the Git URL by clicking `Clone or download`. Save the URL. The URL should look like: `git@github.com:your-username/repo-name.git`

3. Add the Git repository as a submodule in the plugin path:

```sh
git submodule add git@github.com:machikoyasuda/reaction-beesknees-plugin.git imports/plugins/custom/reaction-beesknees-plugin
git submodule init
```

Now you have a separate repository for the files in this plugin directory. Confirm that by running:

```sh
cd imports/plugins/custom/reaction-beesknees-plugin
git remote -v
```

You should see the URL to your repository, and not Reaction.

4. Let's practice making commits to the correct repository by creating a file. Make sure you are in your plugin directory. Then, create a `register.js` file:

```sh
touch register.js
git status
git add register.js
git commit -m "create register.js file"
git push origin head
```

Go to your GitHub repository and you should see your plugin files updated. Now we're ready to get coding.

<- PAGE 2 ->

# Set up your plugin

## In this step, you'll learn

1. Basic plugin directory structure and register.js
2. Loading a plugin: How the Plugin Loader works, how to reset/refresh a plugin
3. How to use RoboMongo, mongo shell

## Set up basic plugin directory structure

1. Now, change into the plugin directory and create a `server` and `client` directory:

```sh
mkdir client
mkdir server
```

The `reaction-beesknees-plugin` directory should have two directories, `/client` and `/server`, and one file, `register.js`:

```sh
  └── imports
     └── plugins
         └── custom
             └── beesknees
                 └── client
                 └── server
                 ├── package.json
                 ├── register.js
 ```

2. The `register.js` adds your plugin to the Registry, the Packages collection in the database.

Initialize a basic `register.js` file:

```js
// register.js

import { Reaction } from "/server/api";

Reaction.registerPackage({
  label: "Bees Knees",
  name: "beesknees",
  icon: "fa fa-vine",
  autoEnable: true
});
```

## Loading the plugin

1. To see load the plugin, run:

```sh
reaction reset -n
```

What does this do? Resetting the database and restarting the app, allowing the app to read the `register.js` file you just created. Registry entries are added when the app first starts, but they don't get reloaded if they already exist.

An alternative option to load a plugin would be to remove that entry directly from the `Packages` collection.

> ProTip: Pass the `-n` flag to `reaction reset` to skip deleting the node_modules folder.

### What's happening underneath?

Reaction's Plugin Loader scans the `plugin` directories for files and checks for three things:

- a `client` directory
- a `server` directory
- a `register.js` at the root of the plugin

When the loader finds these files, it dynamically adds imports so that this code is loaded when the app is launched. Plugins in the `/custom` directory are always imported last, so that they can override default settings.

### How does it look in the database?

Using the`mongo` shell: Confirm that the `reaction-sticky-footer-plugin` has been added to the `Packages` collection by querying the database.

First, in a new Terminal tab, start `mongo`:

```
$ meteor mongo
```

Then, query for the package by name:

```
db.getCollection('Packages').find({name: "reaction-beesknees-plugin"})
```

The Mongo shell should respond with a database record for your plugin:

```
{ "_id" : "TwPi5HoBiHAPtRF56",
"name" : "reaction-beesknees-plugin",
"shopId" : "J8Bhq3uTtdgwZx3rz",
icon" : "fa fa-vine",
"enabled" : true,
"settings" : null,
"registry" : null,
"layout" : null }
```

> To do: Add screenshot of RoboMongo

## Read more

- [Docs: Registry](/developer/packages/registry.md)
- [Blog: An Intro to Architecture: The Registry](https://blog.reactioncommerce.com/an-intro-to-architecture-the-registry/)
