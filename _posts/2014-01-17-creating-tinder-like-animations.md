---
layout: post
title: Creating a cool Tinder like drag animations
description: "How to create a cool Tinder like drag animation for your views"
category: articles
tags: [ios, animations]
image:
  feature: posts/2014-01-17-creating-tinder-like-animations/header.jpg
  credit: Myself
  creditlink: http://guti.in
comments: true
---

## So what are we going to create?

<div style="text-align: center">
<video width="320" height="568" controls><source src="/materials/videos/2014-01-17-creating-tinder-like-animations/almost_done.mp4" type="video/mp4"></video>
</div>

## Creating the draggable view

I'm going to skip all the boring project-structure-creation steps, just make sure you have a clean UIViewController you can work with.
Let's create a new custom view, it'll be our draggable view, I named it `GGDraggableView`. We'll need to add it to our view and set its size. (`GGView` is the view of our main ViewController).
I spiced the view a bit and added a UIImageView to it with a pretty image of Bar Refaeli (find your own image)

<script src="https://gist.github.com/ngutman/8481990.js"></script>

Next step is to add a `UIPanGestureRecognizer` to our view, this is the gesture recognizer we'll use to pump our awesome drag animation.

<script src="https://gist.github.com/ngutman/8482226.js"></script>

This is how the view should look like

<div class="text-center">
    <a href="/images/posts/2014-01-17-creating-tinder-like-animations/1_pretty.png" class="fancybox" title="Pretty view controller">
        <img class="center" src="/images/posts/2014-01-17-creating-tinder-like-animations/1_pretty_320x200.png" alt="Pretty view controller">
    </a>
</div>

## Gesture recognizer logic

When we break down how Tinder handles the pan gesture we notice they change two different parameters of the view during the dragging -

* Rotation
* Scale

To get the desired effect we're going to change these two parameters and link them to the drag "force", which is how long the finger was dragged over the X axis of the screen.
Here's the flow I came up with to get pretty close results -

* Save the original position of the view when the pan gesture starts (so we can change it or 'snap' back to it)
* Find the gesture strength `CGFloat rotationStrength = MIN(xDistance / 320, 1);`
* Calculate the pan gesture 'strength' and calculate the rotation + scale changes
    * The rotation angel is in radians, 2π radians is 360° and we'd want a more subtle rotation, I went for 22.5° (1/16 of a full rotation)
    * `CGFloat rotationAngel = (CGFloat) (2*M_PI * rotationStrength / 16);`
    * The scale will start at 1.0x and go down to 0.93x, so we need to "invert" the force when calculating it -
    * `CGFloat scaleStrength = 1 - fabsf(rotationStrength) / 2;`
    * `CGFloat scale = MAX(scaleStrength, 0.93);`
* Every time the gesture change update the following properties of the view
    * Scale & rotation transformation
    * Position
* When the pan gesture ends animate the view back to the original position and remove the transformations
    * (This is the place we can also issue some kind of action when the user finish the gesture, we'll leave that out for now)

<script src="https://gist.github.com/ngutman/8483077.js"></script>

This is what we have up until now

<div style="text-align: center">
<video width="320" height="568" controls><source src="/materials/videos/2014-01-17-creating-tinder-like-animations/1_first.mp4" type="video/mp4"></video>
</div>

## Adding an overlay and performing action on release
After getting the cool scale & rotation effect we'll probably want to add some kind of 'action overlay' when the image is dragged left / right.
We have several options on where to implement this behaviour, I'll throw that in the same view but be advices that it's probably better to extract this code to a different class.

Our pretty overlay view (I used two images for the different overlay modes)

<script src="https://gist.github.com/ngutman/8483671.js"></script>

<script src="https://gist.github.com/ngutman/8483664.js"></script>

Now let's create and hide the overlay

<script src="https://gist.github.com/ngutman/8483692.js"></script>

And connect it to the gesture recognizer

<script src="https://gist.github.com/ngutman/8483704.js"></script>

We're almost done

<div style="text-align: center">
<video width="320" height="568" controls><source src="/materials/videos/2014-01-17-creating-tinder-like-animations/almost_done.mp4" type="video/mp4"></video>
</div>

## Performing the action

This is the easy part, we just need to check if the distance strength is large enough when the gestures ends.
We perform this check inside the `UIGestureRecognizerStateEnded` case and can do anything we want.

That's about it. Hope you enjoyed and that you're a bit less scared about animations and gestures now :)

Everything is available on [GitHub](https://github.com/ngutman/TinderLikeAnimations). Have fun!

Guti