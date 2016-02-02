---
title: "Managing Shared HTML Components"
---

When you work on a number of different web applications you have a tendency to do some things over and over again. Maybe it's your own calendar widget or some fancy footer you've built. Sometimes, it would be nice to be able to just import those components into your application instead of copy/paste everywhere. When we consider how to set this up, there are a number of things to keep in mind: where will the component live? Should it be in a different source control repository? How will you pull in the dependency at development time? What about build time? Should the dependencies be deployed to their own server and used from there? Or should they get pulled in and packaged with each individual application?

So many questions!

# Source Control

As you would with any piece of software you write, these shared components should be kept in source control. Specifically, they should be kept in their own repository. This allows for the components to have their own lifecycles. Coming from a Java development background, I liken this to any of the Apache Commons libraries. I am certain they live in their own repositories.

I would expect the more the shared components are used, or the bigger your team gets, the more work will need to be done in parallel. So as with all good source control practices, you should use your flavor of parallel development (branches, forks, etc).