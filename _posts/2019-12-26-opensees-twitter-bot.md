---
title: OpenSees Twitter Bot
category: Random Bits
---

Twitter fascinates me. Not only because it's a platform where many academics
disseminate the latest additions to their CVs, but also because it has an
Application Programming Interface (API) through which you can automate all
kinds of stuff.

One cool thing I've come across is bots that automatically retweet and/or
favorite tweets that contain specific keywords or `#hastags`.
This type of automation is possible through the Twitter API.

Unfortunately, APIs usually involve a lot of low level technical details that
require high levels of effort to use. So developers will create libraries to
make APIs more accessible within a specific programming community. Consider
the OpenSees framework, whose C++ classes comprise an API to the finite
element method. The OpenSeesPy library wraps model building, analysis, and
output functions with easy to use Python commands so that earthquake
engineering researchers don't have to deal with low level details of C++.

Knowing Twitter has an API, I Googled how to automatically retweet based on
#keywords. It turns out making a bot is not that difficult using
[Tweepy](http://www.tweepy.org/), a
Python library for the Twitter API. All you need is a Twitter developer
account and an Amazon Web Services (AWS) account, or some other means of
running apps in the cloud. The Twitter developer account is free if all you're
doing is writing a bot, while the AWS account is free for 12 months. That will
give you plenty of time to learn how to run OpenSees on AWS (topic of a future
post) before having to pay a couple bucks a month.

Following this [Tweepy tutorial](https://realpython.com/twitter-bot-python-tweepy/),
I created the [@OpenSeesTweets](https://twitter.com/OpenSeesTweets) bot in about an
hour. The bot monitors the Twitterverse for tweets that contain `#OpenSees` or
`#OpenSeesPy`, then favorites and retweets those tweets. Follow the bot and its
retweets will show up in your feed. Tweet with one of the hashtags and your
tweet will be retweeted into the feeds of the bot's followers. You don't have
to follow the bot for it to retweet your tweets.

Now the real question is, how do we combine Tweepy and OpenSeesPy in a useful
way? Make your OpenSeesPy script tweet when it finishes a response history
analysis? Use the character count from a random tweet to seed your Monte Carlo
simulation? Share your ideas if you like.