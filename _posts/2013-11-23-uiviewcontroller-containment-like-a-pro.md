---
layout: post
title: UIViewController containment like a pro
description: "Annoying bug I stumbled when working on my custom container"
category: articles
tags: [ios, viewcontrollers, bugs]
image:
  feature: posts/view-controllers-like-pros/header.jpg
  credit: Myself
  creditlink: http://guti.in
comments: true  
---


Hey guys,

I'm going to share with you a very annoying problem I had when implementing my cool custom view controller animated container.

I wanted to create a "tabbed navigation bar" that will animate the appearance / disappearance of a `UIViewController` when clicking on the tabs[^1]. Something like this -


<div style="text-align: center">
<video width="320" height="568" controls><source src="/materials/videos/tabbedcontainer.mp4" type="video/mp4"></video>
</div>

I won't dig into the details of how I actually created the container view and implemented the animation but it's not very hard (I'll probably post about it later). The required steps are to implement your own `UIViewController` and use `addChildViewController` to display the wanted view controller, like this -

<script src="https://gist.github.com/ngutman/720db18bbd559eeef251.js"></script>

After executing this code everything seems to work, the container behaved and animated nicely but during the QA session we noticed that the contained view controllers were a bit broken.
The two view controllers we used inherited from `UITableViewController` so they got a scrollable `UITableView`, we noticed that after one "cycle" of animation (switching between the tabs) the `UITableView` frame overflowed.

This video will probably explain it better than I did -

<div style="text-align: center">
<video width="320" height="568" controls><source src="/materials/videos/overflow.mp4" type="video/mp4"></video>
</div>

My immediate thought was “what the hell did I do wrong?” so I began my journey of finding an answer to custom view controller containment failures.
I came upon some cool stackoverflow.com answers[^2] but nothing fixed the problem I had, I succumbed to a *quick and dirty* hack that included changing the UITableView frame on `viewWillAppear:` - this abomination introduced a different bug in another part of the application.

After crying secretly for a bit I really wanted to nail this so I re-read Apple's guide about custom view-contoller containment[^1], and _vuala_ the answer smacked me in the face -

<script src="https://gist.github.com/ngutman/34e7bdd3f233c65ed7e4.js"></script>

Can you guess what I did wrong?

...

You guessed it right, I forgot to set my child view controller frame after adding it :(. One of the responsibilities of a container view controller is to resize the added sub views, after adding that missing piece of code everything started working perfectly :)

So what did I learn from this? Customization can be a pain, but the result is definitely worth it (and the zillion bugs that are created in the process).

Cy’a around.

Guti.

[^1]: _[Apple got nice guides about this subject](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/CreatingCustomContainerViewControllers/CreatingCustomContainerViewControllers.html#//apple_ref/doc/uid/TP40007457-CH18-SW6)_
[^2]: _[Cool stackoverflow answer](http://stackoverflow.com/questions/19038949/content-falls-beneath-navigation-bar-when-embedded-in-custom-container-view-cont)_