---
layout: post
title:  "Build simple telegram bot using ES6 (Part 1)"
date:   2016-07-05 20:42:00 +0300
categories: tutorials
---

# Getting started
In this article i just wanted to tell how i created step-by-step a very simple telegram bot with promise architecture. I will try to fully describe all steps from setting env to working bot. 

Hope, using my notes, you will create a couple useful bots for Telegram and the moment when you read this article will be the great point to start your own army of bots. So, lets go.

# Setup new bot using telegram

For idea of this bot, I was inspired by [this](https://twitter.com/year_progress). (Dude, if you read this - thanks. With your twitter-project, i saw how many time i wasted and decided to write this article).

To get the token for out bot, we need to open [@botfather](https://telegram.me/BotFather) in Telegram.

Simply, start create out bot using `/newbot` command and set it name.

![alt text](/images/build-telegram-bot-part-one/set-bot-name.png "Create and set bot name")

I called the bot like `yearProgress`. After setting name, we will set the username for bot.

![alt text](/images/build-telegram-bot-part-one/set-bot-username.png "Set bot username")

Clearly, it's will be a `@yearProgressBot`.

We finished the main 2 steps and now we will get the token for app.

![alt text](/images/build-telegram-bot-part-one/get-bot-token.png "Getting token")

Succesfully! We are created the new bot, got token and now we can move to project structure and code part.

# Project structure

Now we have the telegram token and we can setup out Node.js-bot-server.

Of course, we will start from command:
`npm init`

This will be our `package.json`.
{% highlight javascript %}
package.json
{
  "name": "year-progress-bot",
  "version": "1.0.0",
  "description": "The bot to feel how many time you waste.",
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

The `node-telegram-bot-api` will using for comfortable work with Telegram API. 

Since we using Babel we need to create `.babelrc`, where will describe our presets:

{% highlight javascript %}
.babelrc
{
  "presets": ["es2015", "stage-2"],
  "plugins": []
}
{% endhighlight %}

Some words about architecture. Sometimes before, i tried to create own bot, but always stumbled with architecture of project. After many searches, i found this incredible [article](https://medium.com/@JonathanZWhite/server-side-infrastructure-when-bots-invade-a2252e9d4bc9#.bka7hyy72).

So, the structure of our project will be simillar to structure described. 

Here it is:

{% highlight language-shell %}
├── src
|   ├── config/
|   ├── handlers/
|   ├── lib/
|   ├── services/
|   ├── utils/
|   └── index.js
├── .gitignore
├── .babelrc
├── package.json
└── index.html
{% endhighlight %}

# Creating the main class

Our main class will look like very little, but very promising:

{% highlight javascript %}
src/index.js
import Messenger from './lib/messenger';

const bot = new Messenger();

bot.listen().then(() => { console.log('Listening'); });
{% endhighlight %} 

Now, go a deeper to the `Messenger` class. 

{% highlight javascript %}
src/lib/messenger.js
import TelegramBot from "node-telegram-bot-api";
import Message from "./message";
import config from "../config"
import InputParser from "./inputParser";
import handlers from "../handlers";

const inputParser = new InputParser();

export default class Messenger {

  constructor() {
    this.bot = new TelegramBot(config.telegram.token, { polling: true });
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

The `listen()` method will run our bot. Using node-telegram-bot-api [library](https://github.com/yagop/node-telegram-bot-api) our bot will have `on` listener and we will handle all text commands which user send to bot-server.

In `handleText(msg)` we are checking the commands from user. Seems, nothing unclear. For this we using `inputParser` class. 

Also, our telegram-token located in `config/` folder.

# Checking messages from user

The `inputParser` class very-very simple (only 2 methods). Of course, we can put these methods directly to `messenger.js`, but one of the points of this arcticle, its show how to create scalable bot.

{% highlight javascript %}
src/lib/inputParser.js
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

Only simple checking. Nothing extra. 
If some of `inputParser` methods returns `true`, we will run the `handlers` methods.

As i said, this bot very naive. We have totally 3 commands: 

1. `/start` - all Telegram bots should have this command. It is the starting point of work with bot.
2. `/progress` - here we will send the year, month, day project.
3. `/help` - some words about "try `/progress` command bla-bla-bla"

So, the `handlers/commands.js` will contains 3 methods too.

{% highlight javascript %}
src/handlers/commands.js
import Time from "../services/time";
import Progress from "../services/progress";

const time = new Time();
const progress = new Progress();

export default class Command {
  constructor() {}

  getGreeting(message, bot) {
    bot.sendMessage(message.from, 'Hi, there! It is nice to see you here, ' + message.user.firstName + '!');
  }

  getProgress(message, bot) {
    let yearPercents = time.getYearProgress();
    let yearProgress = progress.makeProgressString(yearPercents);

    let monthPercents = time.getMonthProgress();
    let monthProgress = progress.makeProgressString(monthPercents);

    let dayPercents = time.getDayProgress();
    let dayProgress = progress.makeProgressString(dayPercents);

    let text =
      "Year:    " + yearProgress + " " + yearPercents + "%\n" +
      "Month: " + monthProgress + " " + monthPercents + "%\n" +
      "Day:     " + dayProgress + " " + dayPercents + "%\n";

    bot.sendMessage(message.from, text);
  }

  getHelp(message, bot) {
    bot.sendMessage(message.from, 'Call the /progress to see how many time you wasted');
  }

}
{% endhighlight %}

In every method we using `bot.sendMessage` method. The first argument - id of telegram user, the second - text, which we will send. I hope everything is clear till this line.

The `lib/message.js` class using for comfortable structure of telegram messages and it have only one static method, which do this simplification.

{% highlight javascript %}
src/lib/message.js
export default class Message {

  constructor(msg) {
    this.from = msg.from
    this.text = msg.text
    this.user = msg.user
  }

  static mapMessage(msg) {
    return {
      from: msg.from.id,
      text: msg.text,
      user: {
        firstName: msg.from.first_name,
        lastName: msg.from.last_name
      }
    }
  }
  
}
{% endhighlight %}

That's all we need to simple bot. 

Left to tell only about data, which we will send to user.

# Generate progress data

To play the functionality like in [year-progress](https://twitter.com/year_progress) twitter, we will create two services. The `Progress` which will generate strings like `▓▓▓▓▓▓▓▓▓▓▓░░░░░░ 69%` and the `Time service`. The last one ill working with the time differences and return all values in percents. Why in percents? Because, it comfortable to generate ASCII-progress bars.

I dont want to focus long on this part, because the functions a very basic.
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
    let yearDays = Math.ceil((new Date(this.currentYear + 1, 0, 1) - this.startDate) / 8.64e7);
    //how many days passed from year begining
    let passedDays = Math.ceil((new Date() - this.startDate) / 8.64e7); // 31
    return Math.ceil((passedDays / yearDays) * 100);
  }

  getMonthProgress() {
    let now = new Date();
    //get month index
    let currentMonth = now.getMonth();
    //get month "length"
    let daysInMonth = new Date(this.currentYear, currentMonth, 0).getDate();
    //how many days passed from month begining
    let daysSinceStartOfMonth = now.getDate() - 1;

    return Math.ceil((daysSinceStartOfMonth / daysInMonth) * 100);

  }

  getDayProgress() {
    let now = new Date();
    let hoursDay = 24;
    let currentHours = now.getHours() || 1;
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
    let max = 15;
    let progress = [];

    percent = (Math.ceil(percent) * max) / 100;
    for (let i = 0; i < max; i++) {
      if (i < percent) { progress.push("▓"); } else { progress.push("░"); }
    }

    progress = progress.join().replace(/\,/g, '');
    return progress;

  }

}
{% endhighlight %}

# Run bot locally

If you kept all points, let's run our bot locally using `npm start`. You should see this: 

![Start bot](/images/build-telegram-bot-part-one/start-bot.png "Create and set bot name")

Let's try to figure out how many time are passed from beginning of year, month, day. For this, open telegram with already developed bot. I wil put the screen with these manipulations: 

![Working bot](/images/build-telegram-bot-part-one/working-bot.png "Working bot")

# Conclusion

As you see, creating of bots - its simple and fun. Frankly, it's my first post and i miss some details. For example, our bot detect time by the machine, where it working. So, if you located in Europe and will put the bot to USA-servers, you will have the different results. 

[Part 2. Creating a simple promo-page and deploying on heroku]()

[Part 3. Unit testing and coverage for out bot ]()
