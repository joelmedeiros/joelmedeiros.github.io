---
layout:     post
title:      What's the PHP role in IoT?
author:     Joel Medeiros
summary:    
categories: php
thumbnail:  
tags:
 - php
 - iot
---

If you are looking for one more article "how to blink a led" or something using PHP, unfortunately it is not your place, here I will guide you to the correct place for PHP in the Internet of Things (IoT) world and also describe what is this IoT world.
To start this conversation we need to know what is IoT and what it does to understand where PHP fits in.  

# What is Internet of Things?

The first definition for IoT was made by [Kevin Ashton](https://twitter.com/kevin_ashton) (incredibly) in 1999:
>  A system where the Internet is connected to the physical world via ubiquitous sensors
 
 How you can see it has more than 20 years and this came up as buzzword only few years ago, that time, we didn't have a worldwide connected in internet. After that, in 2008 working for Cisco Systems Kevin bring another definition:
 
 > Simply the point in time when more 'things or objects' were connected to the Internet than people - [That 'Internet of Things' Thing](https://www.rfidjournal.com/that-internet-of-things-thing)

This definition also brings a strong force for IoT in the market, they believe after that year was when it really get started. 

I'd like to make my own definition for IoT:
> Something in the digital world that changes or interacts with physical world or vice versa

Well, in addition to all these definitions I would say *IoT is an ecosystem*, not only a connected device sharing data to internet, but also receiving data and interacting to other people and other devices to have smart actions, nowadays you can play games capturing monsters based on your cellphone location ([Pokemon Go](https://pokemongolive.com/en/play-where-you-are/)), rent a [bike](https://techcrunch.com/2018/09/13/brazilian-startup-yellow-raises-63m-the-largest-series-a-ever-for-a-latin-american-startup/), [scooter](https://riotimesonline.com/brazil-news/sao-paulo/business-sao-paulo/uber-launches-electric-scooter-rental-in-sao-paulo/) or a [car](https://www.globalfleet.com/fr/technology-and-innovation/latin-america/news/beepbeep-e-car-sharing-kicks-brazil) unlocking by your phone, cities can change light traffic based on traffic, a house can open or close windows based on weather, everything with a unique outcome: make an action in real world.
This ecosystem starts with *data collection* process, collecting data from smart (or not) devices, sending this data through external APIs, processing data from different databases and every bit of information available.
All of these data will be stored in the second level of this ecosystem, *storing data*, for this huge amount of data you may need a data lake, warehouse or a big data structure to store different types of data (json, bat, csv, database data, and so on).
With these data we can do a bunch of things through processing, in this point is where Data Analysis and Artificial Intelligence (AI) take their places, different than what we are used to, we're not only saving data to be processed or analysed by human beings, we are saving information to be processed with other information from different resources to get a quick response based on crossed information. 
A quick example for this is, given a semaphore scenario, where everyday the traffic gets high during the rush hours and we want to control car flux automatically, we can cross information of day-to-day traffic (historical data), current weather (external resources), count of the car's number of the last N minutes, based on these information the data analysis software or AI can create rules for each one of the scenarios below:

- High traffic increased in 50% after 6pm + Raining weather
- Traffic decreased in 10% after 7pm + Raining weather
- Low traffic identified after 10pm + Cloudy weather

Based on these rules, we can determine when we turn on/off the red signal during the peak hours.
Of course, it is not too simple as I am describing, Data Analysis software and AI will need tons of data and a bunch of trainings to achieve this kind of result, it is just an example to understand how we can manipulate data.
 
# What is the difference between automation and IoT?
To define the difference between automation and Iot, we need to understand what is automation, a quick definition is, 

> "Automation is the application of technology, programs, robotics or processes to achieve outcomes with minimal human input" 
- (IBM)[https://www.ibm.com/topics/automation]

With that said, I would like to add to this article that automation also need an human input in the beginning and also it does not expect any changes while it is being processed, which means, that this process will always happen independently of external factors even if it is no longer needed. Like the semaphore example,

<figure style="text-align: center">
  <a href="/assets/images/posts/what-is-the-PHP-role-in-IoT/flowchart-annotation-engine.jpeg" target="_blank">
    <img src="/assets/images/posts/what-is-the-PHP-role-in-IoT/flowchart-annotation-engine.jpeg" alt="How an annotation engine works" />
  </a>
  <figcaption>How an annotation engine works</figcaption>
</figure>

# What about PHP?


# How PHP communicate to things?


# Who is PHP in IoT world?


# Conclusion
