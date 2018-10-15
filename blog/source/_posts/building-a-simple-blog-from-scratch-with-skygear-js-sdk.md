---
title: Skygear tutorial - Building a Simple Blog from Scratch with the JS SDK
date: 2017/09/10 15:18:00
icon: fa-code
categories: Tutorials
tags:
- tutorial
- web
- js
- javascript
---

In this tutorial, we will build a simple blog with Skygear.

<a href="https://skygear.io">Skygear</a> is an open-source serverless platform that has features such as a cloud database, authentication, pubsub, push, chat, and event tracking.

If you are new to Skygear, this is the right tutorial to get started.  You will learn how to save and retrieve records from the Skygear cloud database with the record APIs. Cloud database is the fundamental of most applications nowadays, and Skygear record APIs offer a simple way for developers to interact with the cloud database.
<!--more-->

<a href="https://skygear.io">Skygear</a> sets up the server for you and also provides a set of APIs to save and get data from the database. That means with Skygear, you only have to work on building the interface of the app.

Let's take a look at the features of our web blog app:
<ol>
 	<li>Create and save new blog posts to the cloud</li>
 	<li>List all blog posts stored in the cloud</li>
</ol>
You can play around with the demo we put on <a href="https://tensiuyan.github.io/skygear-tutorial-blog/">GitHub page</a> to get a better idea of how the app works. The source code of this tutorial is available on <a href="https://github.com/tensiuyan/skygear-tutorial-blog">GitHub</a>.
<h2>Add Skygear to the app</h2>
<strong>First, create an HTML file with any text editor.</strong>

In this step, we load Skygear in our app so that we can use it.  For this simple web project, We can simply load it from<strong> the CDN provided by the Skygear Cloud.</strong>

<strong>Add the following code to your HTML file to load <a href="https://skygear.io">Skygear</a>.</strong>

<em>index.html</em>

<script src="https://gist.github.com/tensiuyan/12f7754c48ef1e366416c7156d764e13.js"></script>

Here I have loaded <a href="http://getbootstrap.com/">Bootstrap</a> as well for quick CSS implementation. Booststrap is an open source toolkit for developing with HTML, CSS, and JS. It provides us with decorated HTML component so that we do not have to write CSS from scratch.
<h2>Configure Skygear</h2>
To connect our app with the Skygear Server, we need to configure Skygear.

Make sure you already have a <a href="https://skygear.io">Skygear</a> account. You can get one <a href="https://portal.skygear.io/signup">here</a>. An app project will be created for you automatically after the sign up.

Then, get your app details in the developer portal dashboard. Configure <a href="https://skygear.io">Skygear</a> with the **Server Endpoint** and **API key**.

<img class="alignnone wp-image-37 size-full" src="http://skygearblog.wpengine.com/wp-content/uploads/2017/03/skygear-portal-new.png" alt="skygear-portal-new" />

<strong>Now, create a new JS file in the same directory and add the following lines to it.</strong>

<em>index.js</em>

<script src="https://gist.github.com/tensiuyan/51d48bba0793a61fbb3ec5259ff9e753.js"></script>

<strong>Replace the app end point and  the API key </strong>with the information you get from the developer portal.

<strong>Next, add the following lines inside the body tag of your HTML file so that it loads our JS file.</strong>

<script src="https://gist.github.com/tensiuyan/0cefa3aea66bacc7694cd56ee3bd68be.js"></script>

Now, open your HTML file with a browser and go to the page inspector. <strong>If you see "skygear container is now ready for making API calls" in the console</strong>, it means Skygear has been successfully set up in the app!

Check out the <a href="https://docs.skygear.io/guides/quickstart/js/">quick start guide</a> if you want to know how to set up Skygear in other web environments.
<h2>Implement the basic UI</h2>
Now, it is time to work on the UI and the functions of the app.

The UI of the app is divided into two parts.

The upper part is where we enter the title and the content of a blog post.

The lower part is where we display all the blog posts. And when we click on the blog title, the blog content will be displayed in a new page.

<strong>Add the following lines to inside the body tag in the HTML file.</strong>

<em>index.html</em>

<script src="https://gist.github.com/tensiuyan/1af6565d4db2eff0a5f79679f6dd488c.js"></script>

Refresh your page. You should be able to see:
<ol>
 	<li>Input fields for the post title and the post content</li>
 	<li>"Create Post" button</li>
</ol>
<img class="alignnone size-full wp-image-103" src="https://blog.skygear.io/wp-content/uploads/2017/09/Screen-Shot-2017-09-10-at-10.56.20-PM-2194157830-1505055451599.png" alt="Blog" /> UI of the blog[/caption]
<h2>Sign in anonymously</h2>
After creating the interface, the next step is to save a blog post to the Skygear Cloud DB.

In <a href="https://skygear.io">Skygear</a>, in order to create a record in the database, we have to sign in as a user first. To keep things simple, instead of building an user authentication system where people register an account with their email and password, we will use the sign up anonymously function here.

This means we do not have to sign up explicitly with a username or email. Skygear will keep the user session using the browser local storage.

<strong>Let's add the following code in the JS file.</strong>

<strong>Then call the `signupAnonymously` function right after the API Configuration is completed</strong> so that we will be logged in once the app is initialised.

<em>index.js</em>

<script src="https://gist.github.com/tensiuyan/dda758a68a919216a9a382c5b8611e40.js"></script>
<h2>Create the first blog post</h2>
In this step, we list to a "submit" event of the form `submit-blog-post`. We will also use the title textfield and the content textfield to create a new record in the cloud database `blogpost` table.

<em>index.js</em>

<script src="https://gist.github.com/tensiuyan/c02adf11f1e672e0985437c16d36c945.js"></script>

What we have done:
<ol>
 	<li>Create an event listener to listen for submit events</li>
 	<li>When the form is submitted, the app will first get the input from the title and the content fields, then create a new BlogPost record and associate it with the database table <code>blogpost</code>.</li>
 	<li>Then, save the 'BlogPost' in the `blogpost` table with the two attributes: title and content.</li>
 	<li>Lastly, clear the input fields.</li>
</ol>
Enter a few blog entries to see if the function works. You can check for a log like the one below in the developer console to confirm the record is saved successfully.
<pre class="wrap:true lang:default decode:true">r {_recordType: "blogpost", _id: "c6a3b3a6-2377-4b93-9be2-3a61c5e7be08", _access: e, _transient: Object, createdAt: Tue Mar 07 2017 17:47:48 GMT+0800 (HKT)…}</pre>
*You wouldn't be able to see the blog posts you entered in the app yet. The above lines only help us save the blog post to the cloud. Next we have to retrieve all the blog posts saved from the database and display them in the app.
<h2>List all blog posts from the Skygear database</h2>
<strong>Our next step is to make a query to retrieve the blog posts from the database.</strong>

Add the following lines to the JS file.

<em>index.js</em>

<script src="https://gist.github.com/tensiuyan/08d6cdcf1db30d73921236dbf70204f5.js"></script>

What we have done:
<ol>
 	<li>Create a query to get records from the table <code>blogpost</code>.</li>
 	<li>Add a sorting criteria for the query. `addDescending` sorts the query with descending order according to the created date of each blog post (i.e. _created_at).</li>
 	<li>Update the blog posts we get from the query by adding<strong><strong> </strong>a &lt;h2&gt; element with the blog title, a &lt;p&gt; element with the created date of the blog post, and a</strong><strong>&lt;p&gt; element with the blog content.</strong></li>
 	<li>Add the updated blog posts to the div 'content' we created in step 3.</li>
</ol>
*You can sort the query based on any attribute of a record. Learn more <a href="https://docs.skygear.io/guides/cloud-db/basics/js/#reserved-columns">here</a>.

Cool. We get all our blog posts now. But when should we display them?

<strong>Let's display them after we've signed up and after we've saved a record.</strong>

Now, call <code>showContent()</code> after we've signed up anonymously and after we've saved a record.

<em>index.js</em>

<script src="https://gist.github.com/tensiuyan/7e525a1c14c98fd0398b60484f7d37d0.js"></script>

<script src="https://gist.github.com/tensiuyan/6a7566bb662579ed7721b11aae36e571.js"></script>

Refresh your page now. If everything is in its place, you should see something similar:

<img class="alignnone size-full wp-image-104" src="https://blog.skygear.io/wp-content/uploads/2017/09/Screen-Shot-2017-09-10-at-10.46.49-PM-1.png" alt="Screen Shot 2017-09-10 at 10.46.49 PM.png" border="1px" />

Congratulation! You made it.
<h2>More features</h2>
With Skygear Cloud Database, you can implement a lot more features for this blogging system, for example you can let people sign up with email or Google login with just a few line of code.

Beside Database and User Auth, <a href="https://skygear.io">Skygear</a> also comes with <a href="https://skygear.io/chat/">Skygear Chat</a> for building chat application, Push notification for iOS and Android apps, and Cloud Functions when your app need some server side code.

Check out our website <a href="https://skygear.io">Skygear</a>, to learn more.

What do you think about this tutorial? We publish monthly tutorials. Let us know what you'd like us to write about next!

If you would like support in using these features, get in touch with our team on the [Skygear Slack Channel](https://skygear.signup.team/) or the [Skygear Forum](https://discuss.skygear.io/t/welcome-to-skygear-forum/8).

Co-written by Ten Tang and Zachary Lo