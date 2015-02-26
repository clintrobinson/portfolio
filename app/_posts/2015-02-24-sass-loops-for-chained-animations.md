---
layout: post
title:  "Sass Loops for Chained Animations"
date:   2015-02-24 19:35:40
categories: general
---

One of the lesser used features of Sass would have to be the ability to write a loop to generate css rules for pieces of content. What I am going to demonstrate is how to leverage the :nth-child psuedo selector to create chained/offset animations. I recently made use of this technique on the redesign of [Make Something Edmonton](http://makesomethingedmonton.ca) and am pretty thrilled with how it turned out. It's about 3/4 down the page when the project images animate in, surrounding a block of text.

If you are more interested in looking at the code rather than seeing how it works, feel free to just jump to the [Codepen](http://codepen.io/clintrobinson/pen/qEYLXx/) I have whipped up. Be aware that I am using [Bourbon](http://bourbon.io/), as I do on all of my projects and that is where the includes in my Sass are coming from.  

For the html I have setup 16 divs with a class name of ".square" inside a container with class name ".container". 

    <div class="container is-hidden">
      <div class="square"></div>
      <div class="square"></div>
      <div class="square"></div>
      <div class="square"></div>
      <div class="square"></div>
      ...
    </div>

You'll notice that I have also added a class called ".is-hidden" to the container this is due to the fact that in order use CSS transitions you are required to have a starting value in whatever property you are attempting to animate. We will be using jQuery to remove the ".is-hidden" class on page load and that will in turn trigger the animation. As for right now, lets go through the Sass I have written for this tutorial.

    .container.is-hidden{
      .square {
        @include transform(translateY(-10px));
        opacity: 0;
      }
    }
    .square{
      @include transform(translateY(0px));
      background: red;
      display: inline-block;
      height: 50px;
      opacity: 1;
      width: 50px;
    }
    @for $i from 0 through 16 {
      .square:nth-child(#{$i}) {
        @include transition(all 0.35s ease-out ((0.15)+((0.15)*$i))+s);
      }
    }

The first 6 lines are there as the starting prosition for the properties we choose to animate. I have chosen to animate the opacity and a transform. Lines 7 through 14 are the end positions of the animation as well as the properties that are not being animated (i.e. display, height, width etc.).

It's lines 15 through 19 that tie the whole thing together. @for is the Sass syntax for writing a forloop that is then followed by your variable name that will serve as your counter. the words "from 0 through 16" are arbitrary based on where you want your loop to start and end. 

    .square:nth-child(#{$i}) {

Line 16 is the key to this whole tutorial ".square:nth-child()" probably looks familiar but the "#{$i}" inside the brackets might be foreign. "#{}" is called interpolation and whatever is inside the curly brackets and preceeded by the "#" sign will be treated as plain CSS. This means that every time we iterate through our forloop we will end up with a CSS selector containing ".square:nth-child()" and inside the brackets we will have the current value of "$i" (".square:nth-child(1)", ".square:nth-child(2)", ".square:nth-child(3)" ...).

    @include transition(all 0.35s ease-out ((0.15)+((0.15)*$i))+s);

Line 17 is a Bourbon Mixin for CSS transitions. You just pass in values and they compile to standard CSS inside your generated stylesheet. "all" is the value(s) of the properties you want to animate, "0.35s" is the time to complete the animation, "ease-out" is the timing function of the animation and the mumble jumble you see at the end is simply a bit of math that ends up outputted as the animations delay. 0.15 x (0.15 x $i) creates the offset of animations and really leads to a cool effect. As $i is incremented every time through the loop eac animation ends up starting .15s after the animation prior. Feel free to play with these values and hopefully you will find something that works for you. 

The last few lines of code below are just a little bit of jQuery to trigger the animation by removing the ".is-hidden" class.

    $(document).ready(function(){
      $('.container').removeClass('is-hidden');
    });

Thanks for reading. Feel free to hit me up on twitter [@clintrobinson](ttp://twitter.com/clintrobinson) and I hope to see you around soon as I post some more front-end tutorials.

