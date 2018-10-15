---
title: Skygear 1.0 Released - API Regrouping to Improve Discoverability
date: 2017/08/07 08:34:00
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

We are excited to announce the release of Skygear v1.0. In this release, we've improved the discoverability of APIs to help developers build web and mobile apps more quickly on our serverless platform.

Existing apps will stay on Skygear v0.24. Please remember to pin your SDK at v0.24 or lower as well. Migration documentation for apps developed with v0.24 are coming soon!
<!--more-->

Skygear v1.0's regrouped APIs are the groundwork for many new features and [Skygear v1.1 will be released a week after](https://wp.me/p8xHKD-10) v1.0 with improved user profile discovery. The Skygear portal will reflect the latest v1.1.0 upgrade. All new apps created on Skygear.io will be created with Skygear v1.1.0. You can confirm your version in the **Settings** page of your Skygear developer portal.

[caption id="attachment_61" align="aligncenter" width="1250"]<img class="wp-image-61 size-full" src="http://blog.skygear.io/wp-content/uploads/2017/08/Skygear-v1-1-dashboard.png" alt="serverless baas dashboard open source" width="1250" height="816" /> <em>Go to your developer portal Settings page to verify your Skygear version.</em>[/caption]

## How we have regrouped the APIs
Serverless solutions are designed to help developers build products more efficiently. Knowing what grouped APIs are available will save you hours of creating additional features from scratch. We have regrouped Skygear's APIs into smaller containers so that you can discover them more easily.

We have split our previous container into smaller containers according to the Skygear SDK's 6 key features. This regrouping provides consistency in our API naming for both documentation and for code editors with autocomplete.

For example, If you want to learn about auth related APIs for JS, you can get the whole list from the [auth container documentation](https://docs.skygear.io/js/reference/latest/class/packages/skygear-core/lib/auth.js~AuthContainer.html).

Under the new grouping, APIs are now updated to include the container name. For example, the sign up method `skygear.signupWithUsername` is now updated to `skygear.auth.signupWithUsername`.

Skygear SDK APIs are now grouped under the following 6 containers:

1. PublicDB
1. PrivateDB
1. Auth
1. Pubsub
1. Push
1. Relation

The implications of this regrouping can affect several features. For example, all things to do with passwords and authentication are now grouped consistently under the auth container.

You can find the API references here: [JS](https://docs.skygear.io/js/reference/latest/), [iOS](https://docs.skygear.io/ios/reference/latest/), and [Android](https://docs.skygear.io/android/reference/latest/)

Two additional features that are not included in the SDK can be found on Skygear.io's GitHub repository:
* [Event Tracking](https://github.com/SkygearIO?utf8=%26%2310003%3B&amp;q=event&amp;type=&amp;language)
* [Chat](https://github.com/SkygearIO?utf8=%26%2310003%3B&amp;q=chat&amp;type=&amp;language)

## Continuity of v0.24

We will continue to support v0.24 mainly for bug fixing.

For v0.24 or below users, you can access the v0 documentation and the API references [here](https://docs-v0.skygear.io/).

[Create a new app with Skygear v1](http://portal.skygear.io) and tell us what you think!

**Skygear is an open-source serverless platform. Check <a href="https://docs.skygear.io/">out our docs</a> and set up your free account today!**

If you would like support in using the new features, get in touch with our team on the [Skygear Slack Channel](https://slack.skygear.io) or the [Skygear Forum](https://discuss.skygear.io/t/welcome-to-skygear-forum/8).
