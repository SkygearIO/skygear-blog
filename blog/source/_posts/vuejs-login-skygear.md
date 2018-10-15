---
title: Building a simple login for your VueJS app
date: 2018/10/08 09:00:00
categories: Tutorials
tags:
- vuejs
- vue
- tutorial
- web
- js
- javascript
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/0yA2CQeUSM0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

## Introduction

This tutorial aims to show VueJS beginners how a simple login app can be built with the help of Skygear. Users will have to either sign in or sign up before a secured page can be accessed, which will be a dinner poll in our demo.

The gif below shows what we're hoping to achieve:
![vue-login-skygear-demo](https://i.imgur.com/e7dhiRx.gif)

We'll use VueJS to build our frontend and let Skygear take care of all the backend things. Yes, you read that right. You don't have to set up a server nor a database. Skygear will provide all essential functions, including a user authentication feature which sits behind the login UI.

First of all, clone this [demo project](https://github.com/skygear-demo/vue-dinnerpoll). Although you'll be guided through the basic steps of creating this login app, implementation details of non-login parts (e.g. the dinner poll page) will not be covered. Most example code blocks here are slightly trimmed as well. As a result, you are recommended to read this tutorial with the demo's source code opened side by side. When you need to copy and paste code, use the source code rather than the example code blocks.

For readers who don't wish to create the login app from scratch, you can play with the demo directly. Continue reading might give you a more thorough view on the demo though.

## What is Vue?

[Vue](https://vuejs.org/v2/guide/) is a progressive framework, which means it adapts to your needs. You can plug Vue into just one part of your existing code base and grow it without having to rewrite any existing code. It can be used as a library and a framework depending on your needs.

Vue is a popular choice among developers, described as easy to learn and very approachable by some.

## What is Skygear?

Skygear is a powerful open source backend for mobile, web and IoT apps. Backend tasks such as data storage and management, user authentication as well as push notifications can all be left to Skygear, so that you can focus on your frontend.

An abundant amount of [demos](https://demo.skygear.io), [tutorials](https://blog.skygear.io/category/skygear-tutorials/) and [documentation](https://docs.skygear.io) on Skygear are available, you can refer to them whenever you are stuck.

Despite we are only showcasing our user authentication service here, a lot more features are offered by Skygear. Head over to our [website](https://skygear.io/) for more information!

## Set up the project and configure packages

To kick off quickly, we will use Vue CLI 3 [@vue/cli](https://www.npmjs.com/package/@vue/cli), be sure not to install the old version.

Firstly, create a Vue project called `skygear-vue-login`:

```
vue create skygear-vue-login
```

You'll be prompted to choose some presets. Choose whatever you like, or simply go for the default if you can't be bothered or have no idea what those options are.

Time to add some packages. In order to use the user authentication feature, add Skygear after going into the newly created project:

```
cd skygear-vue-login
yarn add skygear
```

The [bootstrap-vue](https://www.npmjs.com/package/bootstrap-vue) library will be added as well, to save us some time from implementing UI details:

```
yarn add bootstrap-vue
```

While the packages are now added, they have to be configurated. Open the project with your preferred text editor and navigate to `src/main.js`

### Configure Skygear

You'll need your Skygear server endpoint and API key, which can be obtained from your [Skygear Portal](https://portal.skygear.io) once signed up.

Import Skygear and include the following lines:


<script src="https://gist.github.com/tensiuyan/243da36423620e3a4c032a769feb5a14.js"></script>

### Configure Bootstrap Vue

Import Bootstrap Vue and its CSS files, then register Boostrap Vue to your project:


<script src="https://gist.github.com/tensiuyan/34788a06439de86f0772de089df7063d.js"></script>

## Building the app

Below is a tree diagram visualizing the relations between our components:
![vue-login-skygear-tutorial](https://i.imgur.com/hh3no51.png)

Our login app will have two primary pages, 1) the *AuthPage* which simply is the login UI and 2) the *MainPage* which is the dinner poll.

We will focus on the *AuthPage* sub-tree. *AuthPage*, the parent of *SignInForm* and *SignUpForm*, will control its children and manage their switching logic. When a login event is published from the children, it will be passed on to *AuthPage*'s parent - *App*. The *App* component will then take care of it and trigger the login logic.

### SignInForm

Create a `src/components/SignInForm.vue` file and put in the source code:

<script src="https://gist.github.com/tensiuyan/90f14287c217dedf66da5eec67b181b5.js"></script>

Let's talk about the upper UI part first. Components with `b-` prefix are from the bootstrap-vue library. Constructing the *SignInForm* UI with them saves us time from implementing form features like:
1. Alert users when required fields are not filled.
2. Declare a form's submit button by marking it with `type="submit"`.
3. Display error through `invalid-feedback`.

To switch to *SignInForm*, a `swap-form` event is triggered by `@click="$emit('swap-form')"`. Once received by the parent `AuthPage`, the switching logic will be triggered.

Values of the input fields are two-way bound with variables `username` and `password` under the `data ()` code block. This is done by Vue's reactive data binding system.

Once the sign in button is clicked, the submit function `onSubmit` will be called given the values are not empty. This will trigger the Skygear login handler `loginWithUsername` using the `username` and `password` values. A `Promise` will be returned.

If the login credentials can be found on your Skygear database, the `then` block will be executed. A `sign-in` event will be sent to the parent component. The user will then be logged in and navigated to the dinner poll page.

The `user` object is only printed out for now, but surely you can play with it and customize to your needs.

On the other hand, if no matching credentials can be found, the promise will be rejected. If the error returned is `InvalidCredentials` or `ResourceNotFound`, error message "Incorrect username or password" will be propagated via `invalidFeedback` and displayed under the password input field.

### SignUpForm

Create a `src/components/SignUpForm.vue` file and put in the source code:

<script src="https://gist.github.com/tensiuyan/abc52144a9c3106220351b0e36fdcd2e.js"></script>

The *SignUpForm* component is very similar to it brother component *SignIn*. To switch back to the *SignInForm* form, the parent is notified by a `swap-form` signal once again.

Unlike *SignIn* form, the error message is placed under the username input field as the possible error here is duplicated username.

The `then` block of the `signUpWithUsername` handler is identical to the one in *SignInForm*. Upon successful registration, the promise will be resolved and a `sign-in` event will be emitted to trigger login action.

If the error returned indicates the username provided has already been taken, a duplicated username error will be propagated to the password input field via `invalidFeedback`.

### AuthPage

Paste the source code into your newly created `src/components/AuthPage.vue` file:


<script src="https://gist.github.com/tensiuyan/7f9b3b5416df0d8c4241e958a432295f.js"></script>

AuthPage is the parent component managing `SignInForm.vue` and `SignUpForm.vue`.

`swap-form` events emitted by child components are bound with function `swapForm` here, which toggles the variable `wantSignIn`. Form switching is achieved using `wantSignIn` to control the child forms' visibility.

`sign-in` signals received will be passed further on to its parent `App.vue` where all the login and redirect logic takes place.

### Other components

Implementation details of `App.vue` and dinner poll main page `MainPage.vue` will not be covered. Paste the remaining files under the `src/components/` directory from the demo project into yours so that file structure of `src` will look like:

```
|-- src
    |-- components
    |-- AuthPage.vue
    |-- Ballot.vue
    |-- MainPage.vue
    |-- ResultsChart.js
    |-- SignInForm.vue
    |-- SignUpForm.vue
|-- App.vue
|-- main.js
```

## Get the app up and running

Serve our app at localhost:

```
yarn serve
```

You can now play with our freshly created login app!

## Bonus - forgot and reset password

This is a bonus part and the following code has not been included in the demo. Unlike the previous blocks, the code here hasn't been trimmed. Create a new component based on it, or paste it into the existing component with care to try out Skgear's forgot password feature.

<script src="https://gist.github.com/tensiuyan/f03f1cc7b66e3547dbb78e86f646e23a.js"></script>

When the submit button is clicked and the value of variable `email` is not empty, Skygear's `forgotPassword` handler will be called. Once the promise is resolved, a reset password email will be sent to the specified email address.

## Conclusion

We have just created a login app with VueJS and Skygear. Did you notice that you didn't spend a single second messing with the backend, as Skygear has done all of that for you?

You can even check the user records of signed up users at Skygear's web data browser. To explore more on features powered by [Skygear](https://www.skygear.io/), try out other tutorials and demos!
