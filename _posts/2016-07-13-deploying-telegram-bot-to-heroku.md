---
layout: post
title:  "Deploy a Node.js Telegram bot to Heroku, Pt 2"
date:   2016-07-13 21:42:00 +0300
image: /assets/images/deploy-telegram-bot-part-two/preview.jpg
description: In this tutorial we will deploy the telegram bot ...
categories: tutorials
comments: true
---

<img src="/assets/images/deploy-telegram-bot-part-two/preview.jpg" alt="Deploy a Node.js Telegram bot to Heroku, Pt 2" width="100%">

In the [previous part](/tutorials/2016/07/05/build-telegram-bot-using-es6.html) we have created a [Telegram Bot](https://telegram.me/yearprogressbot). We have discovered code steps and launched it locally. Now let release to the Heroku-cloud and make it unstoppable. 

## What is Heroku?

Heroku is a cloud platform for applications. It allows developers to deploy, run and manage applications written in Ruby, Node.js, Java, Python, Clojure, Scala, Go and PHP. All apps on Heroku are running in Dynos - the lightweight Linux containers. By default, if use for free, Heroku provides 550 free dyno hours per month. If you attach your bank card, you receive 450 hours for the month. Totally its 1000 hrs per month. If free dynos not existed, the apps stop until the next month. So it means, you can run one app 24/7. 

Heroku provides access to an application through HTTPS and SSL-certificate, which are important for our Telegram Bot. So, we will choose this platform for this tutorial. Also, it's free for testing, experiments and pet-projects

## Creating a Heroku app

In order to use Heroku, you need to pass [registration](https://signup.heroku.com/) and confirm email. After this click on "Create new app": 

![Creating a new heroku app](/assets/images/deploy-telegram-bot-part-two/create-heroku-app.png "Creating a new heroku app")

Then you need to think about the name for the app and choose the server location, where the app will work. 

![Setting a heroku app name](/assets/images/deploy-telegram-bot-part-two/set-heroku-app-name.png "Setting a heroku app name")

## Preparing project for deploy

In order to run the project on Heroku, let's define the process type for it. For this, we need to create a `.Procfile` in root project directory. Here is it: 

{% highlight bash %}
/* .Procfile */

worker: npm run build && npm run serve
{% endhighlight %}

"Worker" means to handle long-running work on Heroku. Also, for the complex project, I recommend to use tools like pm2 and forever.

## Deploying

I am going to tell shortly about three ways to deploy the app on Heroku. Why shortly - because these ways described more detail in Heroku App dashboard:

![Deploying a Heroku app](/assets/images/deploy-telegram-bot-part-two/deploy-ways.png "Deploying a Heroku app")

You can choose the one of three ways how to deploy: 

1. **Heroku Toolbelt** through terminal
2. **Github**
3. **Dropbox**

Whatever way you choose, we should configure environment variables four our project, because they should contains the telegram token and without them app can't work.

If you choose **Heroku Toolbelt** way you should run in terminal: 

{% highlight bash %}
heroku config:set TELEGRAM_TOKEN=<token-from-botfather-bot>
{% endhighlight %}

If you are going to connect Github or Dropbox you should open "Setting" tab and fill the form with keys:

![Configuring a heroku env variables](/assets/images/deploy-telegram-bot-part-two/config-heroku-vars.png "Configuring a heroku env variables")

## Logs

You can check live logs of application using Heroku Toolbelt with command: 

{% highlight bash %}
heroku logs -t 
{% endhighlight %}

Or using app dashboard:

![Logs in heroku app](/assets/images/deploy-telegram-bot-part-two/heroku-logs.png "Logs in heroku app")

## Wraping up

In this part, I have showed you the summary how to deploy you a bot to Heroku using different ways. The code with `.Procfile` are [here](https://github.com/qpbp/yearProgressBot/tree/part-two). Thanks for reading! Develop and deploy your projects! 

[Part 1. Build a Telegram bot using Node and Babel.js](/tutorials/2016/07/05/build-telegram-bot-using-es6.html)

[Part 3. Unit testing for a Telegram Bot](/)
