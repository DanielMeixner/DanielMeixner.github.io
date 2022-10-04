---
layout: post
title: A layered approach to sustainable application design
permalink: /a-layered-approach-to-sustainable-application-design
---

# tl;dr;
There are multiple areas where we can adjust applicaitions reduce carbon emissions. Some of them might not be relevant to you, others might be out of your authority. It's good to have an understanding that those areas exist - this is where I think the layered approach can help.
At this point (October 2022) there's no wholistic list of recommended patterns to build more sustainable applications. But there's a project on [GitHub](https://github.com/Green-Software-Foundation/green-software-pattern) working on this - let's support it to make it as complete as possible.  

## Designing sustainable applications

When it comes to climate change you might feel that as a software developer you don't really have a lot of things you can do to save the planet.
That' wrong - there is stuff we can do, the layered approach will guide you through the different areas. The bad news is however: As soon as you run your application it will consume energy and therefore impact climate change. So we can't really write "green" software. 

This leaves us with two options:
1. We can try to build applications which are less bad.
2. We can come up with ideas how our software solutions can impact other areas to be more efficient and this way help reduce greenhouse gases.


This post is addressing option 1 - at the same time I want to point out that I think the impact of option 2 can be huge. But not everyone of us is working on this kind of scenario and therefore I want to point out areas where you can make a difference by just focusing on the applications you are working on today.


# A layered model
When designing an application we have to make several choices before, during and after the development. A lot of these areas offer chances to make decisions which are more or less sustainable. I thought about this a lot and came up with this layered model which kind of describes my mental model around it.
This "layer thing" is easy to understand (as a person with software engineering background) and at the same time it helps to focus on specifc aspects at specific times and not always all layers are relevant.
As a disclaimer: I'm thinking about this from a cloud application point of view. So if you are more into mobile development, not all of the layers may be relevant to you (and there might be others).

![A stack of layers decribing different areas where decisions have to be made during software development](docs/images/2022-10-04-layers.jpg)


Let's go through all layers one by one. For every layer I will point out some questions you can ask. 

### UX Layer
Questions to be asked:
- Will my users accept lower resolution images which might save bandwidth? 
- Is my provided service level accurate for the buisness scenario or can I lower it so that I can save resources?
- Will my users accept a reduced performance by reducing compute power? 
### Application Layer
Questions to be asked:
- Can I switch to another framework or language which will be more efficient?
### Architecture Layer
Questions to be asked:
- Can I scale in elastically to free up unused resources?
- Can I increase the density of applications on a single host to improve utilization?
- Can I design my application towards event-driven to avoid unnecessary polling?
- Can I use cloud platform services where I share resources with other customers to increase utilization?
- Can I use scale-out over scale-up to avoid higher clock speeds? 
### Networking Layer
Questions to be asked:
- Can I reduce overall network traffic to save energy during data transfer?
- Can I reduce the distance between client and service to reduce energy during data transfer?
### Data Layer
Questions to be asked:
- Can I use caching to bring data closer to the user to reduce energy consumption during data transfer?
- Can I use different database engines which work more efficently for  specific types of queries?
### Physical Layer
Questions to be asked:
- Can I downsize my infrastructure components to improve utilization?
- Can I move the location of my physical infrastructure towards a location which is running on green energy?
- Can I time shift my computations towards a point in time where  green energy is being used (e.g. solar power)? 
- Can I use a different chipset which will be more efficient for my task (e.g. ARM vs. X86)    
### Governance Layer
Questions to be asked:
- In an enterprise can I cross charge cost for resource consumption so that people using resources will pay for it on a pay-as-you-go basis?
- In an enterprise can I enforce de-provisioning of unused machines?
### Provider Layer
Questions to be asked:
- Can I chose a provider with a green footprint?
### Cultural Layer
Questions to be asked:
- How can I ensure sustainability in software design is a priority for the leadership team?
- How can I ensure sustainability in software is a topic software developers care about?
- Can I build communities around sustainability in software?


## Upsides and downsides

To be clear: All of those topics might impact other areas of your applications. They might touch on reliabilty, useability, customer satisfaction, ease of work, cost, and other. But the same goes for all other aspect we build in our applications. Application design always is based on trade-offs. Sustainability should just be part of the equation.
At the same time I did not touch on stuff like remote work and development processes - even though this might be very important here as well. Maybe I'll add this in the future. 

## Resource to check 
If you are interested in this topic, definitly also check out the following websites which will help you learn more about the impact of software on climate change. It's an interesting read and most of the stuff is open source and open for contributions. 

[Principles Green](https://principles.green/)

[Patterns for sustainable Software](https://patterns.greensoftware.foundation/)

[Sustainable Software Engineering Blog](https://devblogs.microsoft.com/sustainable-software/)

[Green Software Foundation](https://greensoftware.foundation/)

If you read those sites you will find that all of this is somehow interconnected. To me this indicates the information out there in the internet is still very limited today. Let's work on changing this!