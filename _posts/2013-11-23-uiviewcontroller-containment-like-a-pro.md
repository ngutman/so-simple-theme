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

So basically what I wanted is to have a "tabbed navigation bar" that will animate the appearance / disappearance of a UIViewController when clicking on the tabs.

![What I want](/images/posts/view-controllers-like-pros/i1.png)

I won't dig into the details of how I actually created the container view and implemented the animation but it's not very hard. The needed steps are to implement your own UIViewController and use addChildViewController to display the wanted view controller, like this -

<script src="https://gist.github.com/ngutman/720db18bbd559eeef251.js"></script>

After I ran this code everything seems to work for a while until we started experiencing weird bugs, the view controllers we contained were UITableViewControllers and after switching between tabs the internal UITableView frame got screwed and was larger than what it was supposed to be. This caused the UITableViewController to extend below the visible screen and to “overflow” -

<div style="text-align: center">
<video width="320" height="568" controls><source src="/materials/videos/overflow.mp4" type="video/mp4"></video>
</div>

My immediate thought was “what the hell am I doing wrong?” and I began my google journey of finding an answer to custom view controller containment failures. I can tell you that it’s very hard to find relevant information to weird iOS problems, especially when it’s so close to iOS7 release, sometime a weird behaviour is actually an iOS bug (but it’s rare :>).

The 2nd immediate thing I did was to scavenge the official iOS development guides and re-read everything about custom view controller containment, while reading through the "View Controller Programming Guide for iOS” I stumbled upon this code example showing the needed steps when adding a child view controller -

<script src="https://gist.github.com/ngutman/34e7bdd3f233c65ed7e4.js"></script>

Can you guess what I did wrong?

...

You guessed it right, I forgot to set my child view controller frame. One of the responsibilities of a container view controller is to resize the added sub views, after adding that missing piece of code everything started working perfectly :)

Cy’a around.