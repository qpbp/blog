---
layout: post
title:  "What FB Messenger Bot you can do for a 0$ and 15 min?"
date:   2016-08-29 20:42:00 +0300
image: /assets/images/build-telegram-bot-part-one/preview.jpg
description: In this tutorial we will get the cheapest and coolest offers...
categories: tutorials
comments: true
---

<img src="/assets/images/bot-without-programming/preview.png" alt="Build a FB Messenger bot without programming" width="100%">

> Facebook Messenger product manager Seth Rosenberg said that bots can remove the need for people to download new apps on their phones to access services and help make communication with businesses more convenient and organized.

The bots are hype. It`s really true. And the bot can simplify some processes in your business. You can create a simple catalog of your products, bot for communication with the support team, the notifier bot, which information user about new content on your site and etc. You can ask me, but how I can create it? I need to know programming language for this goal! For this, I can answer you, that I am a software developer, I know how to code the bot, but I decided to create my first Facebook bot without programming because it's EASIER and QUICKLY. I really like flights and not against of pretty flights offers. So, I decided to create a simple notifier about new offers from my favorite flights tickets site. While your paid last money and waiting for a pizza you can create something cool and even monetize it. Leme show you how to make something stuff. Take the concern of this article and build yours!

## What is ChatFuel? 

The [Chatfuel platform](https://chatfuel.com/) allows everyone user create a bot without any programming. Only you need: login and create. Uber, TechCrunch, National Geographic created bots base on Chatfuel platform. Is it not sounds impressive? Using this service we will create the bot.

## Registration and giving name

Go to [ChatFuel.com](https://chatfuel.com/) and connect your Facebook account:
![Login](/assets/images/bot-without-programming/login-min.png "Chatfuel login")

After type the name fro your bot and click "Blank Chatbot". You can discover others, already implemented templates:
![Blank bot](/assets/images/bot-without-programming/new-bot-min.png "Blank bot")

## Site overview

Before the bot creating, I want to make a little break and create a simple overview of the [site](http://www.fly4free.com/), where we will take the cheap tickets. We will need this information while creating the sections in bot.

It has a three main categories: The USA, Europe, and Canada. Also, It has the "Last offers" catalog:

![Fly for free](/assets/images/bot-without-programming/flyforfree-two-min.png "Fly for free")

Each category is divided into a countries list:

![Countries](/assets/images/bot-without-programming/flyforfree-one-min.png "Countries")

Ok, forgot this for now. We will take it later. Let's continue.

## Creating groups and block
Before this, please clean all groups on Chatfuel dashboard except "Default" group. It's easy.

Okay, I think we need to start from greetings to the user. Let's change the welcome message block:

![Welcome message](/assets/images/bot-without-programming/default-message.png "Welcome message")

Now go to the "Add Group button" and create new "Navigation" group with "Menu" block:

![Navigation menu](/assets/images/bot-without-programming/navigation-menu.png "Navigation menu")

After, we have "Menu" and we can navigate to it from "Welcome message" block. Just choose the "Menu" block under "Welcome message" block description and type the name:

![Gallery menu](/assets/images/bot-without-programming/menu-gallery.png "Gallery menu")

Super. As we saw earlier, we have "Last offers" category on the site. Definitely, it's the most important information for us because it contains the last amazing offers. We can organize it in "Gallery" card element:

![Gallery menu](/assets/images/bot-without-programming/menu-gallery.png "Gallery menu")

It will look like this:

![Gallery card](/assets/images/bot-without-programming/gallery-card.png "Gallery card")

But what if people want to see the other offers by areas? It's time for the second block. And yeah, give them some text. Using "Add button", add the "Europe", "Canada", "USA" buttons to the second block. So:

![Areas Navigation](/assets/images/bot-without-programming/areas-navigation.png "Areas Navigation")

To use areas navigation, we need to create 3 buttons (areas) to their blocks. Then, create another group calling "Areas". It's better to not have all in a heap. And now I will explain, how we start to get the information about tickets. I will demonstrate it in one example with description step by step:

1. Creating "Europe" block and choose the "Plugins":
![Creating Europe Block](/assets/images/bot-without-programming/creating-europe-block.png "Creating Europe Block")

2. Choose "RSS import" plugin:
![Choose RSS plugin](/assets/images/bot-without-programming/choose-rss-plugin.png "Choose RSS plugin")

3. Open another tab in the browser and go to the site with tickets.
4. Go to "Europe" link.
5. Add word "feed" after slash in address bar and copy new link:
![Address bar](/assets/images/bot-without-programming/address-bar.png "Address bar")
6. Paste it to tab with Chatfuel stuff and enter some related fields: 
![RSS related](/assets/images/bot-without-programming/rss-related.png "RSS related")
7. Connect buttons in "Menu" with new blocks.
![Menu items](/assets/images/bot-without-programming/menu-items.png "Menu items")

That's all. What did we do in step 5? It's called RSS (wiki) and on many-many resources, you can test it, if you paste "feed" or "RSS" after the last slash in the address bar. I said that article will be without programming, so you can google these stuff and educate yourself.

Awesome. Repeat the previous 1-6 instructions to "Canada", "USA", "Last offers" blocks.
P.S. for the "Last offers" the link will be http://www.fly4free.com/feed/ if you stuck.

P.P.S. you can upload images in galleries. But in this tutorial we do without them:

## Connecting with Zapier

To have the latest offers almost immediately we try to use the Chatfuel integration with [Zapier](https://zapier.com). I don't want to discuss Zapier. It's hype too :) And it's very useful to quick bootstrap your product by using any integrations. 

Instruction:

1. Go to "Broadcasting" tab in Chatfuel Dashboard.
2. Create new one in "Autoposting" group:
![Autoposting](/assets/images/bot-without-programming/autoposting.png "Autoposting")
3. Choose "Plugins" -> "RSS (Zapier)"
4. Copy the new Chatfuel Key:
![New chatfuel key](/assets/images/bot-without-programming/new-chatfuel-key.png "New chatfuel key")
5. Go to [Zapier](https://zapier.com) and Sign Up
6. After successful registration click "Make A Zap" somewhere:
7. Choose "RSS" plugin:
![Zapier RSS plugin](/assets/images/bot-without-programming/zapier-rss-plugin.png "Zapier RSS plugin")
8. Click "Save+Continue":
![RSS save continue")](/assets/images/bot-without-programming/rss-save-continue.png "RSS save continue")
9. Paste our main RSS URL and choose the "Different content" in the last select:
![Zapier RSS plugin](/assets/images/bot-without-programming/rss-config.png "Zapier RSS plugin")
10. Click "Fetch & Continue"
![RSS fetch continue](/assets/images/bot-without-programming/rss-fetch-continue.png "RSS fetch continue")
11. Click "Continue"
![RSS test](/assets/images/bot-without-programming/rss-test.png "RSS test")
12. Choose "Chatfuel for Facebook":
![RSS chatfuel](/assets/images/bot-without-programming/rss-chatfuel.png "RSS chatfuel")
13. Click "Test". If all good - click "Save+Continue". If not - click "Test", then "Reconnect" and paste your Chatfuel Key. You should see "Success" message:
![Chatufel test](/assets/images/bot-without-programming/chatfuel-test.png "Chatfuel test") 
14. Here you can tune what data should be sent in a card. Try different. After click "Continue":
![Card config](/assets/images/bot-without-programming/card-config.png "Card config") 
15. You will see an example of data for card. Click "Create+Continue": 
![Card example](/assets/images/bot-without-programming/card-example.png "Card example") 
16. After the successfull test, click "Finish" and give the name for your new Zap.
![Chatfuel success](/assets/images/bot-without-programming/chatfuel-success.png "Chatfuel success") 

That's all! Now Zapier will check the site with tickets every 15 minutes and if found something new, should send all directly into Messenger. On the one hand, it's cool, because of more offers - more chance to find something for you, but on the other hand, many offers can discharge your device :)

## Monetization

What can you do to monetize bot like we built? I found a 3 ways:

1. A **partner links** in Zapier for ticket purchase.
You can look around many travel partner programs and just add a new button in card under new flight offers:
![Partner links](/assets/images/bot-without-programming/partner-links.png "Partner links")

2. A **partner blocks** in menu
As you made all steps up to this point, it's will be simple for you:
![Partner blocks](/assets/images/bot-without-programming/partner-blocks.png "Partner blocks")

3. A **donation block**
You can go to any site, where it's quick to create donation form and paste link to a new block:
![Donation block](/assets/images/bot-without-programming/donation-block.png "Donation block")

Actually, **have you an ideas about** the 4,5... etc points. Could you write about that in comments? 

## Wrapping Up

As you have seen in this tutorial, if you have an idea for a bot you can easily bootstrap it and you don't need any programming or even technical skills. Just take an idea and build the stuff what you need. The Chatfuel + Zapier is very powerfull instruments which help you to achieve this goal.

But it's not all wine and roses. For now your thoughts about sophisticated bot may evaporate, because not all features what you wish now in Chatfuel+Zapier. And for using many features of Zapier you need some money, but for starting bot will be enough the free plan and then you can decide. 

If you have an idea of a complicated bot, need some consulting about bots, your developer is ripping off - contact with me here. Thanks! 

