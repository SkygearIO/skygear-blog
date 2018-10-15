---
title: Skygear Oct updates - Database Dashboard & Skycli
date: 2017/10/25 10:23:00
categories: Feature Releases
tags:
- skygear
- release
- postgreSQL
- skycli
- cloud functions
- database
- dashboard
- cms
---

At [Skygear](https://skygear.io), we're committed to building a serverless solution that is convenient for developers to use while remaining flexible.

This month, we're excited to announce an update for the **Skygear Database Dashboard**. The database dashboard is a convenient interface for developers to make quick edits to database records via the [Skygear developer portal](https://skygear.io).

We also have exciting updates for our Skygear Cloud Functions developers! We've launched [Skycli](https://www.npmjs.com/package/skycli), a new command line tool to deploy Cloud Functions quickly. No prior Git knowledge required!

## Database dashboard - easier way to manage Cloud DB

While developers can manage their Skygear database with any PostgreSQL client, a simpler way to update the records in the database would save a lot of time. Developers can now access the database dashboard in the developer portal.

This is what you can do with the database dashboard.

* Create, update and bulk delete records
* Read system default records
* Export records to CSV file

<img class="alignnone size-full wp-image-115" src="https://blog.skygear.io/wp-content/uploads/2017/10/Screen-Shot-2017-10-25-at-5.19.02-PM.png" alt="" width="1848" height="894" />

<img class="alignnone size-full wp-image-114" src="https://blog.skygear.io/wp-content/uploads/2017/10/Screen-Shot-2017-10-25-at-5.17.17-PM.png" alt="" width="1818" height="894" />
In the future, we want the database dashboard to support the following items as well:

* Create or delete indexes
* Change the schema of the database, such as adding new columns. (However Skygear Database provide a document-like interface so it handle schema automatically when you save records)
* Create or delete foreign key constraints. *(Note: it is now handled by Skygear automatically when you create References between records.)*

For now, you can do the above with direct access from PostgreSQL clients.

## Skycli - Command line tools to deploy Cloud Functions

We have also introduced Skycli, which allows developers to deploy cloud functions with a command line interface. In the past, users had to git push cloud functions to a custom git repository. Now, developers can deploy their cloud functions with just 1 command: `skygear deploy`!

The `skycli` can only be used to deploy the Skygear Cloud Functions at this time, we will add more features in coming months. `skycli` needs to know the account credentials and the name of the app to deploy to.

Step 1: `npm install -g skycli` to install skycli in your app project
Step 2: `skycli login` to log in to your Skygear account
Step 3: `skycli init` to configure the project to a Skygear app (app name)
Step 4: `skycli deploy` to push code to servers

<script type="text/javascript" src="https://asciinema.org/a/ejTSaqnCO8RLsaZcETqiTiKeL.js" id="asciicast-ejTSaqnCO8RLsaZcETqiTiKeL" data-speed="3" async></script>

In Step 3: We also made it easier for developers to quick start by providing cloud function templates. When they run `skycli init`, developers will be asked questions such as whether they are building a JS or Python cloud function. Skygear then creates the relevant files in the project directory.

In the future, we want `Skycli` to support additional features that developers would want in the web portal, such as changing env vars, looking at logs, etc.

For more information, check the Github Skygear repo [ReadMe](https://github.com/SkygearIO/skycli). You can also find the details in the developer portal.

[caption id="attachment_116" align="alignnone" width="1844"]<img class="size-full wp-image-116" src="https://blog.skygear.io/wp-content/uploads/2017/10/Screen-Shot-2017-10-25-at-5.36.32-PM.png" alt="" width="1844" height="899" /> Deploy cloud functions with Skycli.[/caption]

If you would like support in using these new features, get in touch with our team on the [Skygear Slack Channel](https://slack.skygear.io/) or the [Skygear Forum](https://discuss.skygear.io/t/welcome-to-skygear-forum/8).

Last but not least, Happy Halloween 🎃
<iframe class="giphy-embed" src="https://giphy.com/embed/m0epHChbjvsD6" width="480" height="384" frameborder="0" allowfullscreen="allowfullscreen"></iframe>

[**Get started on Skygear for free now!**](https://skygear.io)
