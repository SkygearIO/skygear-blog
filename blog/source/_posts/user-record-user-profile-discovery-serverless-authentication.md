---
title: Skygear 1.1 - Regrouped user record for easier user profile discovery
date: 2017/08/14 06:47:00
categories: Feature Releases
tags:
- skygear
- release
- backend as a service
- serverless app development
- serverless application framework
- serverless mobile development
- serverless web development
- software container
---

Skygear V1.1 introduces regrouped user record increase developer flexibility for handling user management use cases. At-a-glance, the changes will now make a user's username and e-mail a normal record for easier user profile discovery, signing up will only be a single API, and developers can query and update a user easily.

For those of you running apps that require user profile discovery, this release has a major feature change that allows users to update their username and e-mail and have the change saved to their user record, which is easily queried along with other custom user data.
<!--more-->

## Skygear v0.24 user record's security-first design
User data security is always a top priority for the Skygear team. With that in mind, we previously stored authentication data (user name, password, and e-mail) in the `_user` table. While it was possible to discover a user using their e-mail and username, the information in the `_user` table was previously not extensible with custom user data because it was not part of the RecordDB.

[caption id="attachment_70" align="aligncenter" width="606"]<img class="wp-image-70 size-full" src="http://blog.skygear.io/wp-content/uploads/2017/08/skygear_auth3.png" alt="serverless authentication data security" width="606" height="202" /> Regrouped user record makes user profile discovery easier with updated user information.[/caption]

Our intention was to keep authentication data secure and only accessible to the user, but this created extra work for developers and a problem for user discovery.

For example, a user previously could update their authentication (i.e. their e-mail), but this would not be automatically synchronized with their custom user data in the RecordDB; or when user name / e-mail are meant to be sharable in the app, developers needed to work around with the `_user` table.

## Skygear v1.1 introduces extensibility for the user record
We have regrouped a user record to improve user discovery for the convenience of developers to handle user data.

### Username and e-mail is now a normal record

The `username` and `email` are now stored in the user record (i.e. `user` in the RecordDB). It is a normal Skygear record that can be accessed using the RecordDB API. You can also create custom fields in the user record according to the information you want to collect from your users.

Note to Skygear v0.24 users: log in / sign up method returns **a user record**, instead of a `User` object.

The user auth data is now stored in the <code>_user</code> table, which has been renamed to <code>_auth</code>. Only the password and user ID is stored in <code>_auth</code><b>.</b>

### Signing up is now a single API

A new user sign up previously was a two-step process calling two separate APIs. Previously, developers used the signup API to create a new user account then used the Record API to save custom user data. We now have a single API that does both things in one go. See the example below.

This is an example of creating a user with e-mail, password and interest:

<script src="https://gist.github.com/tensiuyan/8531314f3b7bce3cda4bea50ad8b6e92.js"></script>

### Developers can update and query a user easily

Storing the username and e-mail in the RecordDB means that when a user updates their e-mail, the new e-mail is automatically available for user profile discovery.

There is no longer a need to maintain two tables of information on user data (as was the case with v0.24).

Developers can update and query a user easily with the RecordDB API as demonstrated below:

<script src="https://gist.github.com/tensiuyan/2d3a207aab319661eebb74181e6e4289.js"></script>

Find further information in the [User Profile JS documentation](https://docs.skygear.io/guides/auth/user-profile/js/).

Note that this means the developer should handle the security of user information with care. With that in mind, we are already working on Field Based ACL to address data security.

Skygear v1.1 is only available for new apps created through the Skygear Portal. For users who developed apps using v0.24, stay tuned for a coming migration guide.

[Create a new app](http://portal.skygear.io) and tell us what you think of Skygear v1.1!

If you would like support in using the new features, get in touch with our team on the [Skygear Slack Channel](https://slack.skygear.io/) or the [Skygear Forum](https://discuss.skygear.io/t/welcome-to-skygear-forum/8).

**Skygear is an open-source serverless platform. Check [out our docs](https://docs.skygear.io/) and set up your free account today!**
