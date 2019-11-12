---
layout: post
title:  "The Rise and Fall of Greenfield Development"
date:   2019-11-08 19:02:57
category: tech
img: 'placeholders/900x1200.png'
img_thumb: 'placeholders/900x1200.png'
img_alt: 'placeholder'
excerpt: "This post is the background from a talk I gave at Seattle.rb promoting inheritance and a gerund naming convention as an alternative to heavy-handed architectures for solving this problem."
sidebar_extra: >
  <div class="sidebar-extra">
    <blockquote>
      <p>
        Make Ruby natural, not simple, in a way that mirrors life.
      </p>
    </blockquote>

    <p>
      <cite>
        ― Yukihiro Matsumoto
      </cite>
    </p>
  </div>
---

*[PORO]: Plain Old Ruby Object
*[DCI]: Data-Context-Interaction (architecture)
*[MVC]: Model-View-Controller (architecture)

In their paper about DCI, the inventors of MVC captured the essence of what I've learned about architecture, "Clean Code," and software craftsmanship in the last decade:

> While objects capture structure well, they fail to capture system action.
>
> [Trygve Reenskaug and James O. Coplien](https://www.artima.com/articles/dci_vision.html), 2009

This post is the background from a talk I gave at Seattle.rb promoting inheritance and a gerund naming convention as an alternative to heavy-handed architectures for solving this problem.

### The Rise & Fall of Greenfield Development

The thing that first pulled me in to Rails was the now-classic [How to build a blog engine in 15 minutes with Ruby on Rails](https://www.youtube.com/watch?v=Gzj723LkRJY) video. The apparent ability to get an idea out into the world, quickly and with joy, was very exciting at a time when the best options were either hacking on Wordpress or doing "proper" development with a big Java framework.

It also introduced me to Model-View-Controller (MVC) architecture. I didn't know it at the time, but it turns out MVC was envisioned from the beginning to give users a window into your domain modeling.

It is a very simple & natural architecture without many moving parts:

![MVC Architecture](/img/gerunds-a-simple-solution-to-architecture-exhaustion/mvc.jpg)

Even today, after all these years, one of my favorite things is to start a new Rails project for a new idea. It leaves me feeling something like this:



After a while though, if you're not careful, it can feel more like this:


Consider the following chart of project progress over time. The lines represent ideal, actual, and failure scenarios (green, blue, and red, respectively):



The green line represents increasing velocity over time, achieved by creating the right abstractions and building on them, creating new higher level abstractions, allowing ever increasing levels of expressiveness in modeling your domain and the resulting acceleration of progress.

I've never personally achieved the green line. The blue line is more in my wheelhouse. What happens is you get in a groove, powered by new insights, and create models and abstractions to represent the new insights. Then business needs change, a large client with lots of money wants something you haven't considered before, or you learn that you actually modeled something poorly and need to refactor. Velocity stalls as adjustments are made, but after making it right again, picks back up. Rinse, repeat, and over time velocity is generally good and progress is steady.

Then there's the red line. This is where maybe your lead engineer declares something ridiculous like "With our ambitious timeframe we don't have the luxury of automated testing!" 🤮 Right. Or overwhelming copy-pasta, rampant premature abstraction, skipping the "make it right" step in the "make it work, make it right, make it fast" advice, etc.

Or, actually, maybe you get into red line territory because Rails gave your team just enough rope to hang yourselves with.

Back in the day people crammed lots of logic into controllers. Quickly recognized as a bad idea, "fat model, skinny controller" came into fashion, which maybe was better, but without any other direction it resulted in hugely bloated domain models, especially core models like User or Product.

OK, so Rails was missing something? Where do we look for advice?

### Popular Architectures

_Data, Context, Interaction_ (*DCI*) by Trygve Reenskaug and James O. Coplien, 2009

My experience with DCI was that it was trending a few years ago, but I don't know anyone personally who implemented it. I think people realized they were solving an important problem, but didn't want to figure out how to translate it to their project. For me, I started playing with extending objects with behavior at runtime.

I won't go into the details of DCI except to point out what is in my opinion the most useful jargon: "barely smart" models and "interactors" to hold your business logic. I think the rest of the stuff they introduce are essentially supporting actors to the model+interactor core duo.

_Clean Architecture_ by Robert Martin, ~2012

While I do try to generally follow Robert Martin and enjoyed "Clean Code" many years ago, I don't have much experience with his newer "Clean Architecture" blog series and book. I mostly want to point out how, in my opinion, there's a lot of glue code to support the most important layers: "entities," aka data models, and "interactors," which hold business logic and even have the same purpose and name used by DCI.

_Trailblazer_, an open source gem, 1.0 in 2015

This project gem-ifies a lot of the same architectural layers sussed out in DCI, "Clean Architecture," and the Ruby+Rails community at large over the years. Generally speaking I think it's a good capstone to years of thinking and implementing architectural solutions from many sources. It has very thoughtful organization and great separation of concerns.

However, it doesn't appear to avoid the problem with architectural solutions in general. Like others, it requires large buy-in for a maturing codebase since it uses its own unique folder structure and introduces a lot of new jargon to a project. I can't tell you it's not a great idea for your project, but it's definitely too much for even the largest of mine.

### Architecture Exhaustion 😭

Because of all the tradeoffs I'd have to make for the benefits of a structured architecture, this all leads me to a form of analysis paralysis. I'm just not that excited:

* It's a BIG jump from greenfield Rails/MVC to DCI, "Clean Architecture," or Trailblazer.io
* Requires strong buy-in & commitment from everyone, especially when new trendy opinions seem to come along every few years.
* Adds jargon to domain models
* System behavior not easily discernible from file/class structures due to jargon-based organization.
* Relies heavily on delegation, which is not at all as great as advertised. More on this later.

*Summarizing Architectural Insights*

All architectures considered here:

* Advocate for something between completely bare to "barely smart" data. Nobody talks about "fat models" anymore.
* Have a layer for system behavior, which I consider the most essential layer.
* Have misc layers for plumbing - presenters, view models, contexts, etc

For the latter two, I'd summarize it as "*transience*":

1. Temporary roles/behavior engaged in by domain models
2. Temporary representations of domain models for display

