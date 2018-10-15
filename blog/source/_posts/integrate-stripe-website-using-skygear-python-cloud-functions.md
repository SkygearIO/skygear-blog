---
title: Integrate Stripe into your website using Skygear Python cloud functions
date: 2018/08/21 11:39:00
icon: fa-code
categories: Tutorials
tags:
- stripe
- payment
- tutorial
- web
- js
- javascript
---

![](https://i.imgur.com/AguXZCV.png)

TL;DR: This is an introductory tutorial on how Stripe can be integrated into your website using Skygear Python cloud functions (lamdba function in specific). If all you want to see is demo codes, check out the repo here: https://github.com/skygear-demo/skygear-cloud-py-stripe.

A payment gateway has to be set up first before you can receive payment from your users. Stripe by far is the most popular choice because of its intuitive APIs, well-written docs and incredible customer support.

Say we have an online coffee bean shop and we'd like to accept payments through Stripe. Our checkout flow will be:

1. A customer has added some coffee beans to the cart and clicks the *Check Out* button.

1. A payment form powered by Stripe pops up.

1. The customer completes it, then clicks *Pay $XX.XX* button.

1. The payment details are sent to Stripe's server for validation.

1. Once the details pass the validation, a token will be returned and your server can use it to charge the payment.

You are probably now thinking: "Wait, you'll need a server to actually charge a payment? I don't have one!". Need not to worry, Skygear provides Cloud Function Hosting for Python and Node.js routines, which means you do not have to go all the way setting up a server just to handle Stripe payment.

Besides, since Skygear comes with a PostgreSQL database, in case you want to store the transaction details, simply save them to the database provided with our convenient APIs.

In this tutorial, we will implement the following:

1. A simple web interface where users can purchase coffee beans with their credit card through Stripe. Consisted of some welcome texts and a *Pay with card* button, as shown in the gif below.

1. A Python cloud function hosted on Skygear that processes payments with tokens returned from Stripe. If a payment is charged successfully, its order details will be saved to your Skygear database.

The pop-up payment form is done by Stripe so we don't have to handle the sensitive credit card information.

![](https://i.imgur.com/2ES9OTk.gif)

## Step 0: Environment setup

Let's set up both Skygear and Stripe in our web app first.

Pull up your favorite text editor, and create `index.html` in a `skygearStripe` directory.

There are three scripts we have to include in the `&lt;head&gt;` - Skygear's CDNs, Stripe's script, and our own `index.js` which will be created later.

<script src="https://gist.github.com/tensiuyan/04137f66fae87e150063e5b8dba99ebd.js"></script>

Then, create `index.js` under the same directory to configure Skygear and Stripe.

<script src="https://gist.github.com/tensiuyan/969c4b379f8b00aa90440cbd54887e68.js"></script>

Both Skygear endpoint and API key can be obtained from your Skygear Developer Portal https://portal.skygear.io/. If you already have a Skygear account, sign in; If you don't, simply sign up for free.

Similarly, navigate to https://dashboard.stripe.com/account/apikeys to get your Stripe API key. If you don't have a Stripe account yet, create a free one.

Please note that using your `Publishable key` to configure Stripe will result in a charge failure `secret_key_required` later. Instead, use your `Secret key` or other `Restricted API keys` with correct access rights.

Cool, we are now all set! Let's start working on web app's UI.

## Step 1: Implement the web app

Apart from a welcome title and a paragraph describing our product, we will create a *Pay with card* submit button which triggers a pop-up payment form by Stripe once clicked.

<script src="https://gist.github.com/tensiuyan/dfc0f2e743d85c0c9a1fdff4da86986d.js"></script>

Let's apply some styles to make the form prettier. Put the following lines into the `&lt;head&gt;`.

<script src="https://gist.github.com/tensiuyan/db8311d9111553e541c3d8dfbba93e1c.js"></script>

When a user clicks the *Pay with card* submit button, we should `open` the Stripe handler created earlier. This will result in a Stripe payment form popping up, with payment params we have specified. We should also `close` it upon page navigation.

<script src="https://gist.github.com/tensiuyan/1260942658d97bd42dde72696d38ba58.js"></script>

## Step 2: Pass the order information to our Python cloud function

As mentioned, payment request is made from our server with a secret token we obtained from Stripe when users submit their credit card information.

Let's first create a function that passes all required information to our server side before diving into the server codes.

Once the credit card information submitted by a user passes the validation on Stripe's server side, a token will be returned to our client-side web app. We can then access it in the handler's callback function in our `index.js`

Four parameters will be passed using Skygear's `lambda` function
- the secret token generated by Stripe
- the email of the customer
- the product name
- the product price

<script src="https://gist.github.com/tensiuyan/42fbd71be71cd2eb0cffa4762c41a41e.js"></script>

When you call `skygear.lambda('process_payment', params)`, a corresponding cloud function named `process_payment` in Skygear cloud will be called.

We can now proceed to implement our server-side code.

## Step 3: Set up the cloud function environment

We are going to create the `process_payment` cloud function in Python and deploy it to Skygear cloud with Skygear command line tool - `skycli`.

The `process_payment` cloud function does two things:

1. Send payment request to Stripe
2. Create a payment record in your Skygear database upon payment success

To start with, let's create `__init__.py` in `skygearStripe` and insert the following lines to configure Skygear.

<script src="https://gist.github.com/tensiuyan/a4ece43b123d76eb4e0ff7e2cb2435c0.js"></script>

Here we have created a Skygear container with the master key of our app. Using the master key means you have entered the *god user mode* and can do whatever you want to. Master key is required for making user-required API call in Skygear cloud.

This container is initialized because records of payment details will be saved to Skygear database later and we need a user account to do so.

Also create a `requirements.txt` in the same folder with just one line:

```
stripe==1.82.1
```

The Skygear cloud environment by default has Skygear installed. However, to use other libraries (Stripe in this case), you'll need to specify them in `requirements.txt`. All the dependecies in `requirement.txt` will then be automatically installed for you.

## Step 4: Make a payment request to Stripe

Finally, we can start writing the `process_payment` cloud function!

As this cloud function is triggered by the `skygear.lambda('process_payment', params)` line from our client-side web app, we will declare the mapping with the following decorator syntax:

```
@skygear.op(name, user_required=False, key_required=False)
```

`name` refers to the name of the cloud function. `user_required` and `key_required` are optional and by default `False`. If you want to know how to work with them, check out the doc [here](https://docs.skygear.io/guides/cloud-function/lambda/python/).

Now, we will call the Stripe API `stripe.Charge.create` to make a payment request. Insert the following lines into `__init__.py`.

<script src="https://gist.github.com/tensiuyan/2e90a0c3212bae0c0be6a1bcd8052020.js"></script>

The `process_payment` cloud function accepts 4 parameters:
- `product_name`
- `product_price`
- `stripe_token`
- `user_email`

which are exactly the ones passed by `skygear.lambda('process_payment')`.

Since this is just for demo purpose, exceptions are only caught by a simple block of code, returning result as `failed`. The Stripe doc recommends catching different types of exception separately. Learn more [here](https://stripe.com/docs/api#error_handling).

## Step 5: Create a record in the Skygear database

Next, we'd like to create a record in the Skygear database when a payment is charged successfully.

To create a Skygear record in the cloud, you have to set at least two things:

1. `_access`: the Access Control List (ACL) of the record
2. `_id`: the id of the record. Skygear Id is consisted of two parts: `type`/`string`, where type refers to the table name. In the example below, we are saving a record to the `payment_record` table.

Insert the following lines in the `process_payment` cloud function, just below the `except` block of Stripe charge:

<script src="https://gist.github.com/tensiuyan/5b36672eea5f64d187eba8ce0b145122.js"></script>

Please note that Skygear migrates the schema automatically for you. If the server cannot find a table named `payment_record`, it will create one automatically. The same applies to record fields.

Hurray! Our codes are ready. The very last step is to push the codes to the Skygear cloud.

## Step 6: Push codes to Skygear cloud using `skycli`

skycli is a command line tool for deploying Skygear Cloud Functions. To install skycli globally:

```
npm install -g skycli
```

Then login with your Skygear account's email and password.

```
skycli login
```

In the directory `skygearStripe` where your `__init__.py` is located, run:

```
skycli init
```

You will be asked to connect the current directory with an app on your Skygear cloud.

Skygear cloud also supports static asset hosting. If you want to host the web app on the Skygear cloud as well, say yes when you are asked to create a static hosting directory.

Choose `Empty` when you are prompted to select a project template. As we have already set up the `__init__.py`, we do not need the template.

Your original directory structure:

```
|-- skygearStripe
    |-- __init__.py
    |-- index.html
    |-- index.js
    |-- requirements.txt
```

will become

```
|-- skygearStripe
    |-- public_html
        |-- index.html
        |-- __init__.py
    |-- index.html
    |-- index.js
    |-- requirements.txt
    |-- skygear.json
```

once the initialization is completed.

Replace the `index.html` in `public_html` directory with the outside one we have been working on. Also move `index.js` into the `public_html` directory. In general, you should put all your static files into the `public_html` directory.

Then deploy with the following command:
```
skycli deploy
```

If the build is successful, you will be able to access the web app at https://&lt;your-app-name&gt;.skygear.com/static/index.html. Navigate to the web app and test if the cloud function is running properly!

When a payment is made successfully, you should have a record stored to the `payment_record` table. You can easily visualize your Skygear database at your Skygear Portal.

![](https://i.imgur.com/ig5glO9.png)

That's it! If you would like support in using these features, get in touch with our team on the <a href="https://slack.skygear.io">Skygear Slack Channel</a> or the <a href="https://discuss.skygear.io/t/welcome-to-skygear-forum/8">Skygear Forum</a>.

Edited by Elliot Wong
