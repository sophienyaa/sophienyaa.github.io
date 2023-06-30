---
layout: post
title: Microservices inside Salesforce With Platform Events and Change Data Capture,
  Part One.
date: 2020-12-12T15:00:00.000+00:00
categories: salesforce microservices
image: "/img/23b9a76b8b2a24bf3937cd648179c24f_badge.png"

---
![Header Image](/img/23b9a76b8b2a24bf3937cd648179c24f_badge.png)

Normally, when I do a talk at Dreamforce, or another conference, I write a follow up blog post covering the same content. But the end of 2019 was pretty busy with [Dreamforce](https://www.salesforce.com/blog/dreamforce-2019-infographic/), [Monkey Run Morocco](https://www.theadventurists.com/adventures/monkey-run-morocco/), [French Touch Dreamin](https://frenchtouchdreamin.com/) and Christmas/Holidays. I figured I'd get it written up in early 2020...

Then 2020 happened... and turned in to a complete dumpster fire. With everything going on, I was finding it difficult to find the time and motivation to write anything. With the year almost over, and my blog revamped I knew I wanted to get it written.

Enough boring excuses. Here we go.

This is part one of the follow up post to my talk "Microservices inside Salesforce With Platform Events and Change Data Capture". If you did catch my talk at Dreamforce 2019, or London's Calling 2020, I hope you enjoyed it. If not (or even if you did!), read on as I will cover the same topics here.

In part one we will cover the concepts, and in part two, the implementation. The first thing we need to understand is...

### What are microservices?

The key to microservices is kind of in the name; small (micro) services. These services generally cover a specific functional area, or function and perform only that function. (e.g generating documents, or handling payments).

Ideally, these services are independently deployable, maintainable and testable. In larger applications or organisations these services are often developed and maintained by different teams, even using different languages or tech stacks. They are loosely coupled to other services and shouldn't have any 'hard dependencies' on them.

When grouped together, all of these services, and their supporting infrastructure make up a larger application (e.g a Billing System).

A good analogy is the robots in a car factory. You have robots to weld the cars, robots to paint them and so on.  The painting robot is independent of the welding one. It does not know how to weld, or care how the car was welded. All it does is paint the car and send it on to the next station (micro service) in the factory (application)

![](/img/abb-paint-atomizer.jpg)

One of the key benefits of this is it means that when it comes time to upgrade the welding robot, the painting one does not need to be changed. Likewise if the painting robot needs to be changed, the welding robot doesn't care.

As you've probably experienced, changing chunks of a normal 'monolithic' type application can be painful, as it's often tightly connected to other parts of the application with no easy way to change just the one part you need to.

With a microservice approach, often these issues are either removed entirely, or heavily mitigated.

This is great and all, but these micro services need a way to communicate with each other, this needs to be in a 'common language' they can all understand, so this brings us to...

### What is an event-driven architecture?

So as alluded to above, this approach hinges on these services having a common way to talk to each other.
If I've got a service written in Apex on Salesforce, it has to know how to talk to a service written in NodeJS on Heroku, or something written in Java running on-prem.

This is where an event-driven architecture comes into play. It means that all of these services share a common eventbus on to which they publish (or push) to and subscribed to (or consume from), normally these events are segregated into topics or channels. I am going to use the terms publish, subscribe and topic to describe this from here on out.

The event bus sits in the middle of all of these microservices, services produce and subscribe to it to move data between them and perform their functions. The event bus also often manages things like queueing, replay of events, access to topics and security. Some common event buses are Apache Kafka, MQTT and CometD.
Salesforce of course also has its own event bus, the Enterprise Messaging Platform (often just called Platform Events, which uses CometD)

![](/img/9d0edf028a3e0056757e93a3244a43de_salesforce-event-bus.jpeg)

To better understand this concept, let's think for a second about twitter, it is like an event bus. If I see #Salesforce trending on twitter, I might click on that hashtag. This would mean I am 'subscribing' to the Salesforce 'topic'. If I then tweet "#Salesforce is awesome" I am then 'publishing' to Salesforce 'topic', and anyone else following #Salesforce (aka subscribing to that topic) will see that message. Your microservices work the same way.

Each service will publish and subscribe to topics it 'cares' about in order to function. It doesn't really have any knowledge of the other services, it just grabs the information it needs to do its job, and publishes its results for other services to do the same. This of course allows for services to be swapped/changed/enhanced without causing impact to other services within the application.

Another key feature of an event driven architecture is the ability for multiple subscribers to subscribe to the same topic. Often we only want one service producing to a topic, but there are exceptions to this rule.

For example, if I am building a billing system, perhaps I need to perform some tasks when an Opportunity is closed/won.
I might need to generate a PDF invoice to send to the customer or I might need to generate a packing slip to send to a warehouse.
If all of these subscribe to the 'Opportunity' topic, then all of them can perform their task independently when they receive a closed/won opportunity message on that topic. Likewise, if I have one source of truth for Opportunity data (Salesforce) then I don't want other systems publishing data to the Opportunity topic.

If in the future I also need to activate the customer's new service in another system, the new system can simply listen to this topic! We don't need to make any changes to the existing systems, we can just 'bolt on' this new system.

All of this sounds exciting right? So...

### How does this relate to Salesforce?

Salesforce has a number of tools to help us out here, under the banner of 'The Salesforce enterprise messaging platform'. This is often just called 'Platform Events', however Platform Events are just one of the tools available to us.

There are two we are going to cover in detail here, Platform Events and Change Data Capture.

So let's start with...

#### Platform Events

Platform Events in this context is the term salesforce gives to custom topics. e.g topics we are developers are able to define and use for whatever we like.

We can define a platform event in much the same way as a Custom Object. They have a name like "My_Cool_Event__e" and have various field types that can be added to them (text, numbers, checkboxes, etc) which will contain the data for subscribers to use, or for publishers to populate.

Once we have defined our platform event 'topic', we can then publish events to it. This can be done from within Apex code, from processes or flow, or via Salesforce APIs from external services.

We can also subscribe to events from Apex triggers, processes, flows, lightning and visualforce components, or in external services via CometD.

Platform events are the most flexible kind of event, as they can be defined to contain any information we want, and be published or subscribed through a number of different methods.

There however is another very useful kind of event, these are...

#### Change Data Capture

Change data capture (CDC) is a feature of Salesforce that publishes 'change events', These events represent changes to Salesforce records. Things that are classified as changes, and thus are published include creation of a record, updating a record, and deleting or undeleting a record. Once enabled for an object, these events are automatically published by Salesforce.

Change data capture is similar in a lot of ways to Platform Events and, in fact, uses the same underlying bus. The main difference is that publication of these events is done automatically by salesforce, and the layout of data within them (their schema) is unable to be changed by us. We can, however, subscribe to these events in the exact same way as a Platform Event.

Why is CDC important? Well it means that we can subscribe to any change in data across Salesforce, so in our billing example above, we don't need to define our own 'Opportunity' event, we can simply enable CDC and subscribe to the events we need.

Now that we know about CDC, we need to...

#### Put it all together

We know that the 'Salesforce Enterprise Messaging Platform' is our event bus. It is a part of Salesforce, and our publishers and subscribers (aka our micro services) need to be able to access it. For services on platform, it's easy.

There is nothing special that needs to happen (aside from creating Platform Events or enabling CDC), you just need to build them. It's slightly harder with Services outside of Salesforce. They need users accounts, and the appropriate level of access and your micro services needs to understand how to publish or subscribe to events (more on this in part two...)

We also know that our topics are Platform Events (if defined by us) or Change Data Capture (defined by Salesforce). We know that we can subscribe to both CDC and Platform Events in a variety of ways both on and off platform. We also know that we can publish Platform Events in a number of ways both on and off platform. Finally, we know that CDC events are published for us by Salesforce (once enabled).

### Conclusion and Further Reading

In part one, we covered the main concepts, microservices and event driven architecture. How they work together and how they apply to Salesforce with Platform Events and Change Data Capture.

In part two we will cover how to build an application with these concepts, including how to swap out a service within that application to an off-platform version!

I hope you've found this informative, and if you've got any questions or comments, please send me a tweet [@mickwheelz_](https://twitter.com/mickwheelz_). If you want to learn more in the meantime, check out the links below;

[Salesforce Blog - Which Streaming Event Do I Use?](https://developer.salesforce.com/blogs/2018/07/which-streaming-event-do-i-use.html)

[Trailhead - Platform Event Basics](https://trailhead.salesforce.com/content/learn/modules/platform_events_basics)