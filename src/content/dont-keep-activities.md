---
layout: post
title: "Don't keep activities"
author: Angus
tags: ["Android"]
date: "2018-05-06T23:46:37.121Z"
draft: false
---

Android has a relatively unpredictable habit of killing any `Activity` that is not immediately visible to the user to reclaim memory. Because of this we need a way to test how our app behaves when our Activities are killed. 'Don't keep activities' forces Android to kill background activities (as though it were in a low memory environment).
From the [Android Developer Documentation](http://developer.android.com/tools/debugging/debugging-devtools.html):
> Tells the system to destroy an activity as soon as it is stopped (as if Android had to reclaim memory) This is very useful for testing the `onSaveInstanceState(Bundle)` / `onCreate(android.os.Bundle)` code path, which would otherwise be difficult to force. Choosing this option will probably reveal a number of problems in your application due to not saving state. For more information about saving an activity's state, see the Activities document.

Don't keep activities is in the **Developer options** settings of your device.

*Consider enabling this to test that your app saves state properly.*