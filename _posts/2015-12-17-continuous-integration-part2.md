---
title: Continuous Integration/Deployment - Part 2
subtitle: What Features Do We Need?
---

![Continuous Integration/Continuous Delivery](/img/continuous.jpg){: style="display: block; margin-left: auto; margin-right: auto;" }

When we start looking at Continuous Integration (CI) or Continuous Delivery (CD) tools there are a number of them to choose from. So which one makes the most sense? This will be a comparison of some of the major enterprise level tools and my opinion on which one makes the most sense.

# What Features Do We Need?

Before we even look at the tools that are available, let's first look at some of the features that we would expect the CI/CD tool to have. This list does not include the basics of what CI/CD tools should do - like detecting code changes, building and testing code, delivering feedback, and deploying code.

__Project Templates__

For the projects or applications that we are defining within the tool, it would be nice to have the ability to specify a template that the application would go through. This would define typical steps like, shutdown, start, restart, deploy, etc. The actual definition of the step may or may not already be defined. Additionally, it would be beneficial to be able to define templates for certain application types. For example, a web application that gets deployed to WebSphere Application Server.

When the template needs to be updated, it would be beneficial if the tool would allow for all of the usage of the template to be updated as well.

__Global Reporting__

Being able to measure and learn from those measurements is a vital piece of the puzzle when attempting to understand how something is used and whether or not something is valuable (whatever that means to you).

For CI/CD tools, here are some of the metrics that might be beneficial to measure. This list may change depending on your use cases.

 - Team utilization
 - Team retention
 - Process performance

For application specific reporting, we could also include these items

 - Test success rates
 - Test coverage rates
 - Static code analysis stats

__Extensible and Customizable__

One of the primary needs for most people, me included, is to not get rid of the tools I already use. That means that whatever CI/CD tool we decide to implement must work with the existing tools that we have. That being said, I realize that some of the tools we use are built in-house. Therefore, there will not be integration points for those tools. We will need this CI/CD tool to support creating custom integrations so that we can hook up our existing tools.

__Dependency Management__

Typically when developers think about dependency management, they think about dependencies between our application and it's libraries. This really isn't all that different. Instead of dependencies between our application and libraries, I want to be able to see the relationship between our application and all of the other applications that it uses. 

When one of the dependencies of our application changes, it should be able to trigger an automatic rebuild of our application. This will allow us to know sooner when a dependency will potentially cause breaking changes within our application.

A nice-to-have for a CI/CD tool would be a visual map of the dependent applications. I would love to see this map showing the current build status of each dependency.

__Security__

There likely will be a large variety of people who use the tool that will have different needs. For example, we will likely have a team that is in charge of creating and maintaining the templates that are used by application development teams. We will have development teams that need to setup their specific application, testing teams that might configure automated tests, and then we will have management, customers, etc that might only need read-only access to see the reports and analytics that the tool provides. The tool needs to be able to support these roles.

Additionally, there may be a situation where an application is sensitive in nature and therefore should be isolated from other groups. We will need the ability to hide jobs from specific roles and groups.

__Delivery Pipelines__

As we plan on using this tool to deploy code, we will need the ability for the tool to perform those deployments. The infrastructure for those deployments could vary. This plays along with the idea that the tool needs to be extensible - we need to be able to support various platforms.

__On-Premise Installation__

Due to the nature of our business, we need for the solution to on-premise based. This will admittedly eliminate a number of high quality CI/CD tools from the list, but hey, it's what we need to do.

__Notification Channels__

We will need feedback on how an application is fairing through the steps of it's lifecycle within the CI/CD tool. This feedback will need to be sent to teams using various communication or notification channels. This could include, but should not be limited to, email, text message, [hipchat](https://www.hipchat.com), [campfire](https://campfirenow.com/), and [slack](https://slack.com/).

Each application definition will likely be different from the others. The notification channels should be configured at the application definition level.
