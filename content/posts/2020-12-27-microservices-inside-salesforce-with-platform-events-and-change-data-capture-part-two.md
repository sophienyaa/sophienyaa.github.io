---
categories: ["note"] 
title: Microservices inside Salesforce With Platform Events and Change Data Capture,
  Part Two
date: 2020-12-27T13:00:00.000+00:00
tags: 
  - salesforce
  - microservices
image: "/img/23b9a76b8b2a24bf3937cd648179c24f_badge.png"

---
![Header Image](/img/23b9a76b8b2a24bf3937cd648179c24f_badge.png)

Now that the holidays are over, its time for Part Two! Haven't read [part one](https://mickwheelz.net/salesforce/microservices/2020/12/12/microservices-on-salesforce.html "part one") yet? Check it out and then come back, don't worry, I'll wait for you.

⌚️

Finished? Great, let's go!

### How does this work on Salesforce?

As we discussed in part one, we can take advantage of the Salesforce enterprise messaging platform to build 'microservices' in Salesforce.

Before we go any further, these microservices are a little different from traditional ones. As we know, Salesforce provides a platform as a Service. This includes everything we need, including the database, the runtime, the language, etc. As we know, we can _only_ use the tools provided by the platform. We can't just deploy some random java to our salesforce org (yet... 😉) so we must write our on-platform services in Apex* and we use the database we are given*

\*(yes there are other languages/databases (e.g JS in Aura/LWC and External Objects... but that's out of the scope of this post!)

So in reality, our services are discrete 'chunks' of Apex that don't have dependancies on other code, or potentially even objects in Salesforce (aside from the Platform Event definition they use).

We still get most of the same advantages as traditional microservices. We can however, have microservices in our Application that are _outside_ of salesforce, and these can behave as traditional microservices, with their own language, database, runtime, etc. We will cover these too!

So let's take a look at how we do this...

### On Platform

Let's think about the example from part one. We have a simple billing system that uses Opportunities and Opportunity Line Items. When an Opportunity is closed/won, we want a PDF invoice (with the line items listed on it) generated and attached to the Opportunity.

Using the 'old' way, you might have a trigger on the opportunity that uses VisualForce to generate the PDF and attach it to the Opportunity. This runs on synchronously on save and you may eventually run into limit issues with this approach.

So how do we do it using microservices? Well let's take a look at the below diagram

![](/img/image12.png)

If you want to see the example code for this approach, you can check out the repo [here](https://github.com/mickwheelz/DF2019-Invoicing-Salesforce "here") and it is referenced below.

You can see here we have three 'services';

1. The Apex trigger `OpportunityChangeEventTrigger`, is on the Opportunity Change Data (CDC) event, so it isn't run synchronously. It simply it calls the   `OpportunityChangeEventService` class to fires the Invoice Request Platform Event (PE) that contains the information needed to generate the Invoice. This approach is taken to decouple the next service from the Opportunity.
2. The Invoice generation service. This listens for the Invoice Request PE in the `GenerateInvoicePDFTrigger`, then calls the `InvoicePDFService` and it uses VisualForce (`InvoicePDF` / `InvoicePDFController`) to generate a PDF invoice from the information contained in the event. It knows nothing about the original opportunity (this is important!) When it is finished it simply fires a 'Invoice Response' PE. It ONLY generates PDFs, nothing else.
3. Finally, we have `InvoicePDFTrigger` that listens for the Invoice Response PE and then calls the `InvoicePDFService` so that it can insert an attachment record. It doesn't know or care where the PDF actually came from.

None of these services have any hard dependencies on each other. They do their small individual jobs and that is it. They also all run asynchronously so the user doesn't need to sit and wait for them to complete for their record to be saved.

Want to see how this looks under the hood? Well you can check out the code here

So what happens when our little VisualForce PDF generator service stops scaling or is otherwise unfit for purpose? Well, we simply replace it with something...

### Off Platform

We've decided we are going to use a small service running on Heroku to generate our PDFs now, so we've written something in NodeJS and deployed it. Let's take a look at how we switch to using this now.

If you want to take a look at the code for the Heroku service, you can see it [here](https://github.com/mickwheelz/DF2019-Invoicing-Heroku "here"), It is referenced below.

> ![](/img/image11.png)

You can see we again have three services, and the first and third have not changed. The code is identical. We have ONLY replaced the middle service.

The NodeJS is very simple, in `index.js` the `begin()` function it logs in to Salesforce (to access the event bus) and then begins listening for Invoice Request PEs. If it receives  one, it calls `processGenerateInvoicePDFEvent()` which uses [PDFKit](https://pdfkit.org/ "PDFKit") to generate the invoice PDF, this is handled in `generateInvoice.js`

When it is finished generating the PDF, `fireInvoicePDFEvent` is called to fire the Invoice Response PE for to be picked up by our attachment service inside salesforce.

To handle the Salesforce requests, we are using the excellent [JSForce](https://jsforce.github.io/ "JSForce") library.

Now that we have our little PDF service running, I bet you are wondering how we switch to using it.

We disable `GenerateInvoicePDFTrigger` ... that's it. Seriously.

![](/img/screenshot-2020-12-27-at-11-57-43.png)

If we want to switch back, all we need to do is shut down our Heroku service and switch the trigger back on. Simples!

Of course, in the real world we would likely remove the trigger from production once we had finished migrating to the new service, but you can see how simple it is with this approach.

Now that we've see how we can create microservices on and off platform, and swap them at will we should...

### Wrap Up

The event driven paradigm is a very flexible one and allows things to change without the usual level of friction. It can speed up development, and allow teams to work in parallel. It can even speed up the end user experience, as everything is done async. It is an excellent option for large and complex Applications, but it is not a silver bullet.

It does introduce more 'moving parts', for smaller, simpler applications this may not be worth it. It is also worth considering the [limits of platform events](https://developer.salesforce.com/docs/atlas.en-us.platform_events.meta/platform_events/platform_events_api_considerations.htm), for very large volumes you may need to move to a different event bus, such as [Apache Kafka](https://kafka.apache.org/intro). Salesforce cannot natively talk to Kafka, but you can use middleware like MuleSoft or you can build your own bespoke services to do so (a topic for a future post perhaps... 🤔)

You should **always** take time to consider what is the right approach for what you are building, both now and in the future. However I hope this post has informed and inspired you to think about how you could use this architecture in Salesforce!

If you've got any questions or comments, please send me a tweet [@mickwheelz_](https://twitter.com/mickwheelz_)