---
title: Continuous Integration/Deployment - Part 1
subtitle: What Is Continuous Integration/Deployment?
---

![Continuous Integration/Continuous Delivery](/img/continuous.jpg){: style="display: block; margin-left: auto; margin-right: auto;" }

According to [Martin Fowler](http://martinfowler.com), Continuous Integration is...

> ... a software development practice where members of a team integrate their work frequently, usually each person integrates at least daily - leading to multiple integrations per day.

And Continuous Delivery is...

> ... a software development discipline where you build software in such a way that the software can be released to production at any time.

# Continuous Integration (CI)

The concept of Continuous Integration has been around since 1991, first coined by Grady Brooch in what is now known as the Brooch method. It was later adopted by the process of Extreme Programming in 1999. Continuous Integration really came into place because integration was something that used to take a long time. Sometimes it took months for different aspects of an application to integrate together. Continuous Integration resolves the complexity of integrating by doing it more frequently and resolving the issues as they arise. By not waiting until the end, Continuous Integration decreases the amount of change necessary to successfully integrate systems. 

Using a tool to perform Continuous Integration is not strictly necessary. It is only necessary that developers continually integrate their code with dependent systems as they develop. However, it is beneficial to have a Continuous Integration server that is always working to make sure all of the code works with the dependencies. This server eliminates the need for developers to remember to integrate their code - it is now done automatically. If something goes wrong, the Continuous Integration server will notify the appropriate groups that something is wrong.

Continuous Integration tools have matured over the last couple decades to be able to detect changes within source control. Based on those changes, the CI tool can download, build and test the code. Once the process is complete, it can notify a group of stakeholders the results.

# Continuous Delivery (CD)

Continuous Delivery is an addition to Continuous Integration. It takes the built artifacts from the integration and deploys them and runs more tests. Continuous Delivery also comes with a little bit of a culture change - the team prioritizes keeping the software deployable over working on new features. This actually isn't a huge leap. Keeping the code in a deployable state should come almost as second nature - just like integrating the code at regular intervals.

The tests that are part of CD are automated functional tests. These tests exercise the application after deployment to make sure it works. This is different than the test that executed as part of CI which only tested the individual units of the application.

In order to achieve Continuous Delivery you need to be able to say that you have push button deployments of your applications to any environment and on-demand. This high level of automation in your deployments is what allows for them to be tacked onto Continuous Integration. This full set of automation is generally referred to as the Deployment Pipeline. Additionally, it is generally advisable to have a good and collaborative working relationship with everyone involved with building, testing and deploying the application. Having that collaboration is generally part of a DevOps culture (DevOps to be covered in a completely other set of articles).

Continuous Delivery provides benefit by reducing deployment risk. If we make the changes small and continually deploy them through the environments, then there will be less of a chance that something will go wrong. If something does go wrong, it is easier to figure out and resolve.

__Differences with Continuous Deployment__

Sometimes people will confuse Continuous Delivery with Continuous Deployment. The real difference between the two is that Continuous Delivery ensures that all code **can** be deployed to production whereas Continuous Deployment **will** deploy all code to Production. Continuous Delivery requires approvals from the business to actually affect production.
