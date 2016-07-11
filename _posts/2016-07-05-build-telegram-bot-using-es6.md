---
layout: post
title:  "Build a Telegram bot using Node and Babel.js, Pt 1"
date:   2016-07-05 20:42:00 +0300
categories: tutorials
comments: true
---

<img src="/assets/images/build-telegram-bot-part-one/preview.jpg" alt="Build a Telegram bot using Node and Babel.js, Pt 1" width="100%">

Telegram is a big messaging platform, allows users to quickly exchange messages. In June 2015 Telegram team announced the bot platform, so it meant a new market for developers. Using different server-based technologies you can build your own bots with various functionality that can resolve serious business issues and processes or just be fun

In this article, step-by-step, we will build a simple telegram bot using Node.js and Babel.js. The bot will send the how much time was passed from the begining of year, a month, a day. I hope you could create a couple useful bots for Telegram using my notes, and the time spent reading my article will start a great point to begin your own army of bots. So, let's go.

## Getting token for a new bot

To begin, we need to get the token. For this, you need open [@botfather](https://telegram.me/BotFather) bot in your Telegram app. The [Botfather](https://telegram.me/BotFather) is a chief among all bots and with it, you can create your bots. To create a new bot, type the `/newbot` command in this bot. I called this bot like `yearProgress`.

![Creating bot](/assets/images/build-telegram-bot-part-one/set-bot-name.png "Creating bot")

After setting name, we will set the username for the bot. All telegram bots should end with word "bot". Based on this, let's call it like `@yearProgressBot`.
![alt text](/assets/images/build-telegram-bot-part-one/set-bot-username.png "Set bot username")

We finished the main 2 steps and now we will get the token for app.

![alt text](/assets/images/build-telegram-bot-part-one/get-bot-token.png "Getting token")

Successfully! We have created the new bot, have got a token and now we can move to project structure and code part.

## Setup Node project

Now we have the telegram token and we can set up our Node.js-bot-server.

Once you're ready, we'll create our project. Open your terminal and type the next in your "Projects" folder:
{% highlight bash %}
mkdir yearProgressBot && cd yearProgressBot
{% endhighlight %}

We created a new folder. To init the Node.js project in folder, run the following:
{% highlight bash %}
npm init
{% endhighlight %}

You will see several questions in the terminal to answer. After finish, `package.json` will appear in the folder which will contain some dependencies for our project.
I already prepared all dependencies for project, so only copy and paste these lines to your `package.json`:
{% highlight javascript %}
/* package.json */

{
  "name": "year-progress-bot",
  "version": "1.0.0",
  "description": "The bot to feel how much time you waste.",
  "main": "src/index.js",
  "scripts": {
    "start": "nodemon src/index.js --exec babel-node",
    "build": "babel src -d dist --presets es2015,stage-2",
    "serve": "nodemon dist/index.js"
  },
  "dependencies": {
    "node-telegram-bot-api": "^0.21.1"
  },
  "devDependencies": {
    "babel-core": "6.10.4",
    "babel-polyfill": "^6.0.16",
    "babel-preset-es2015": "6.9.0",
    "babel-preset-stage-2": "^6.5.0",
    "babel-register": "^6.9.0",
    "nodemon": "^1.9.2"
  },
  "author": "Roman A",
  "license": "ISC"
}
{% endhighlight %}

In `scripts` property are located 3 npm-scripts:

*   `npm start` - will run project and if any changes will rerun server
*   `npm build` - will compile project files into `dist/` folder
*   `npm serve` - will run complied server in `dist/` directory

The `nodemon` package will help us to quickly rerun project. `Node-telegram-bot-api` will be using for comfortable work with Telegram API.

As a Node.js 6 isn't stable yet and not supports all Ecmascript features, we will use Babel. Babel is a compiler which support the latest Javascript features. With some plugins, you can use the latest syntax right now.

We need to create `.babelrc` in root directory, where will describe our presets for Babel:

{% highlight javascript %}
/* .babelrc */

{
  "presets": ["es2015", "stage-2"],
  "plugins": []
}
{% endhighlight %}

Now let’s install all packages:
{% highlight javascript %}
npm install
{% endhighlight %}

## Structure
Here it is:

{% highlight bash %}
├── test
├── src
|   ├── config/
|   ├── handlers/
|   ├── lib/
|   ├── services/
|   └── index.js
├── .gitignore
├── .babelrc
└── package.json
{% endhighlight %}

We will put all project files in `src` folder, because Babel will compile these files to another `dist` folder. I attached the explanation about folders:

*   **test/** - will contains the unit-tests for an application
*   **src/config/** - for configuration files
*   **src/handlers/** - to handle Telegram commands 
*   **src/lib/** - for classes, which will resolve the commands
*   **src/services/** - will contains reusable methods

## Creating the main class

The main class will locate in `src/index.js` and will be the "heart" of our bot.

{% highlight javascript %}
/* src/index.js */

import Messenger from './lib/messenger';

const bot = new Messenger();

bot.listen().then(() => { console.log('Listening'); });
{% endhighlight %} 

We created an instance of `Messenger` class and called the `listen()` method which runs the bot. Let's, go a deeper to the `Messenger` class in `src/lib/messenger.js` file. 

{% highlight javascript %}
/* src/lib/messenger.js */

import TelegramBot from "node-telegram-bot-api";
import Message from "./message";
import config from "../config"
import InputParser from "./inputParser";
import handlers from "../handlers";

const inputParser = new InputParser();

export default class Messenger {

  constructor() {
    if (process.env.NODE_ENV === 'production') {
      this.bot = new TelegramBot(config.telegram.token, { webHook: { port: config.telegram.port, host: config.telegram.host } });
      this.bot.setWebHook(config.telegram.externalUrl + ':443/bot' + config.telegram.token);
    } else {
      this.bot = new TelegramBot(config.telegram.token, { polling: true });
    }

  }

  listen() {
    this.bot.on('text', this.handleText.bind(this));
    return Promise.resolve();
  }

  handleText(msg) {
    //format the message
    const message = new Message(Message.mapMessage(msg));
    const text = message.text;

    //checking if asked "/progress"
    if (inputParser.isAskingForProgress(text)) {
      return handlers.command.getProgress(message, this.bot);
    }

    //checking if asked "/start"
    if (inputParser.isAskingForGreeting(text)) {
      return handlers.command.getGreeting(message, this.bot);
    }

    // default - send message with help
    return handlers.command.getHelp(message, this.bot);
  }
}
{% endhighlight %}

Consider in more detail our constructor. We can run bot in two ways - using **getUpdates** or **Webhooks**. I would recommend visiting the [Official Telegram FAQ ](https://core.telegram.org/bots/api#getting-updates), where you can read more about differences. We will use the Webhooks in production and getUpdates while developing. Why we should using Webhooks in production I’ll tell you in the next post.

To start work with Telegram API we need to create an instance of [node-telegram-bot-api library](https://github.com/yagop/node-telegram-bot-api). Accordingly with environment we will create an instance with different params. Webhoooks requires a port, a host, and a link, where our application will work. Important note, that for Webhooks, Telegram requires, that your site should have a certificate and working well through https://. 

All configs are located in `src/config/index.js`.

{% highlight javascript %}
export default {
  telegram: {
    token: process.env.TELEGRAM_TOKEN || 'yourtoken',
    externalUrl: process.env.CUSTOM_ENV_VARIABLE || 'https://yoursite.com',
    port: process.env.PORT || 443,
    host: '0.0.0.0'
  }
};

{% endhighlight %}

## Checking messages from user

The `inputParser` class have only 2 methods for 2 Telegram commands: 

1. `/start` - all Telegram bots should have this command. It is the starting point of work with bot.
2. `/progress` - here we will send the passed time.

We can put these methods directly to `src/lib/messenger.js`, but one of the points of this post, its show how to create a scalable bot. These methods will check which command has сome:

{% highlight javascript %}
/* src/lib/inputParser.js */

export default class InputParser {

  isAskingForProgress(text) {
    const pattern = "/progress";
    return text.match(pattern);
  }

  isAskingForGreeting(text) {
    const pattern = "/start";
    return text.match(pattern);
  }
}
{% endhighlight %}

Only checking. Nothing extra. If some of `inputParser` methods returns `true`, we will run the `handlers` methods.

Commands class in `src/handlers/commands.js` contains 3 methods. These methods react on incoming messages:

{% highlight javascript %}
/* src/handlers/commands.js */

import Time from "../services/time";
import Progress from "../services/progress";

const time = new Time();
const progress = new Progress();

export default class Command {
  constructor() {}

  getGreeting(message, bot) {
    bot.sendMessage(message.from, 'Hi, there! It is nice to see you here, ${message.user.firstName} !');
  }

  getProgress(message, bot) {
    const yearPercents = time.getYearProgress();
    const yearProgress = progress.makeProgressString(yearPercents);

    const monthPercents = time.getMonthProgress();
    const monthProgress = progress.makeProgressString(monthPercents);

    const dayPercents = time.getDayProgress();
    const dayProgress = progress.makeProgressString(dayPercents);

    const text =
      "Year:    " + yearProgress + " " + yearPercents + "%\n" +
      "Month: " + monthProgress + " " + monthPercents + "%\n" +
      "Day:     " + dayProgress + " " + dayPercents + "%\n";

    bot.sendMessage(message.from, text);
  }

  getHelp(message, bot) {
    bot.sendMessage(message.from, 'Call the /progress to see how much time you waste');
  }

}
{% endhighlight %}

In every method, we using `bot.sendMessage` method from Telegram` method from Telegram Library. The first argument - id of telegram user, the second - text, which we will send.

That's all we need for a bot. 

It remained tell only about data, that we will send to the user.

## Generate progress data

To play the functionality like in [year-progress](https://twitter.com/year_progress) twitter, we will create two services. The `Progress` which will generate strings like `▓▓▓▓▓▓▓▓▓▓▓░░░░░░ 69%` and the `Time` service. The last one will be working with the time differences and return all values in percents. Why in percents? Because, it comfortable to generate ASCII-progress bars.

I don't want to focus long on this part because the functions a very basic.
Services with comments, on stage!

{% highlight javascript %}
services/time.js
export default class Time {

  constructor() {

    this.currentYear = new Date().getFullYear();
    this.startDate = new Date(this.currentYear, 0, 1);
  }

  // all function return values like 0 to 100
  getYearProgress() {
    //days in the current year
    const yearDays = Math.ceil((new Date(this.currentYear + 1, 0, 1) - this.startDate) / 8.64e7);
    //how much days passed from year begining
    const passedDays = Math.ceil((new Date() - this.startDate) / 8.64e7); // 31
    return Math.ceil((passedDays / yearDays) * 100);
  }

  getMonthProgress() {
    const now = new Date();
    //get month index
    const currentMonth = now.getMonth();
    //get month "length"
    const daysInMonth = new Date(this.currentYear, currentMonth, 0).getDate();
    //how much days passed from month begining
    const daysSinceStartOfMonth = now.getDate() - 1;

    return Math.ceil((daysSinceStartOfMonth / daysInMonth) * 100);

  }

  getDayProgress() {
    const now = new Date();
    const hoursDay = 24;
    const currentHours = now.getHours() || 1;
    return Math.ceil((currentHours / hoursDay) * 100);
  }

}
{% endhighlight %}

{% highlight javascript %}
services/progress.js
export default class Progress {

  constructor() {}

  makeProgressString(percent) {

    if (!percent) {
      throw new Error("Miss argument");
    }
    if (percent < 0 || percent > 100) {
      throw new Error("Value should be beetween 0 and 100");
    }

    if (typeof percent != "number") {
       throw new Error("Should be a number");
    }
    //max the 15 blocks
    const max = 15;
    let progress = [];

    percent = (Math.ceil(percent) * max) / 100;
    for (let i = 0; i < max; i++) {
      if (i < percent) { progress.push("▓"); } else { progress.push("░"); }
    }

    return progress.join('');

  }

}
{% endhighlight %}

## Run bot locally

If you kept all points, let's run our bot locally using `npm start`. You should see this: 

![Start bot](/assets/images/build-telegram-bot-part-one/start-bot.png "Create and set bot name")

Let's try to figure out how much time are passed from beginning of year, month, day. For this, open telegram with already developed bot. I wil put the screen with these manipulations: 

![Working bot](/assets/images/build-telegram-bot-part-one/working-bot.png "Working bot")

## Conclusion

As you see, creating of bots - it's simple and fun. Frankly, it's my first post and I could skip some details. As an example that our bot uses local machine time. So, if you're located in Europe and put the bot to USA-servers, you will have the different results. My example is working on Heroku server, which located somewhere in Europe. Therefore there can be little delays while messaging.

You can see the working an example [here](https://telegram.me/yearprogressbot) and start build your
own bot from the [repo](https://github.com/qpbp/yearProgressBot/tree/part-one).

P.S. For an idea of this bot, I was inspired by [this](https://twitter.com/year_progress). Dude, if you read this - thanks. With your twitter-project, I saw how much time wasted and decided to write this article.

Part 2. Deploying Telegram Bot to Heroku - soon
