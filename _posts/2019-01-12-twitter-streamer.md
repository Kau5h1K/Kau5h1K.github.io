---
layout: post
title: "Twitter Streamer"
date: 2019-01-12
tags: [Topcoder, real-time, python, stream, Twitter, API, web-scraping, parse, Auth, Text Analysis, Tweepy]
excerpt: "Interact with APIs in a deep dive into the Twitter streaming API. We look into streaming real-time Twitter data and to analyze and visualize it!"
comments: true
badge: Concept
badge-color: "cyan"
---
Herein, we're going to look at:
* How to stream data from **Twitter API**
* How to filter incoming tweets for keywords
* Principles of **API authentication** and **OAuth**
* `Tweepy` Python package

---

One of the first major differences between the **Twitter API** and most of the other APIs is that you can access other APIs anonymously. But Twitter requires that you have an *account*. In order to gain access to the Twitter API,
* One needs to create a Twitter account if you don't already have one.
* Log into *Twitter Apps* and Click `Create a New App` - *[Terms and Conditions applied ].*
* Then go to your `Keys and Access Tokens` tab and copy your **API key** & **API secret**, your **Access Token** & **Access Token secret**. These are the *authentication credentials* that will allow you to access the Twitter API from python.

>For *pedagogical* reasons, we'll do a mock run-through of how you would stream data and analyze without having to explicitly create an account.

---

It is important to mention that Twitter has a number of APIs.
Firstly, they have **REST APIs**, which is short for *Representational State Transfer*.
Twitter's REST API allows users to `read and write Twitter data`.

<img src="https://i.imgur.com/fjuUVg5.png" alt = "REST API" width="500" height="200" />
In order to `monitor and process tweets in real-time`, that is to stream Twitter data, however, we'll want to use the **Twitter's Streaming API**.
![alt text](https://i.imgur.com/n6bZpED.png "STREAMING API")
In particular, we'll use the **public stream**, which Twitter's API documentation states as `Streams of public data flowing through Twitter`. The Public stream itself contains a number of options. As we want to read and process tweets, we'll want to use the **GET statuses/sample API** which `Returns a small random sample of all public statuses`.
![alt text](https://i.imgur.com/n3HiSop.png "GET STATUSES/SAMPLE")
If you wanted to access absolutely `All public statuses`, you would need to use **Twitter's Firehose API**, which is not publicly available and would most likely cost you a pretty penny.
![alt text](https://i.imgur.com/3mRygI9.png "FIREHOSE API")
One last point to note before we begin streaming tweets:
Tweets are returned to us as **JSONs** and they contain numerous possible fields. For more information, check out the *Twitter tweet Field guide.*
![alt text](https://i.imgur.com/N38REla.png "Twitter tweet Field guide")
You can get Tweet text, user, language, time of tweet, among many other fields.
![alt text](https://i.imgur.com/wKHHRyt.png "Twitter tweet Field guide-2")

---

Let's see how to access and stream data from the Twitter API.
I usually recommend the package `tweepy` which has a nice balance of usability and capability.
Let's now use it to stream some tweets.
First off, it has an `OAuth handler` which takes care of all that nasty stuff for you: all you need to do is pass the *API key* and *secret* to the handler and then to pass the *access credentials* using `set_access_token` method.
```python
# Import package
import tweepy

# Store OAuth authentication credentials in relevant variables
access_token = "1092294848-aHN7DcRP9B4VMTQIhwqOYiB14YkW92fFO8k8EPy"
access_token_secret = "X4dHmhPfaksHcQ7SCbmZa2oYBBVSD2g8uIHXsp5CTaksx"
consumer_key = "nZ6EA0FxZ293SxGNg8g8aP0HM"
consumer_secret = "fJGEodwe3KiKUnsYJC3VRndj7jevVvXbK2D5EiJ2nehafRgA6i"

# Pass OAuth details to tweepy's OAuth handler
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
```
After this, you'll need to define a Twitter stream listener class.
```python
class MyStreamListener (tweepy.StreamListener):
    def __init__(self, api = None):
        super(MyStreamListener, self).__init__()
        self.num_tweets = 0
        self.file_name = "tweets.txt"
        self.file = open("tweets.txt", "w")

    def on_status(self, status):
        tweet = status._json
        with open(self.file_name, 'a') as file:
            file.write(json.dumps(tweet) + '\n')
        self.num_tweets += 1
        if self.num_tweets < 100:
            return True
        else:
            return False

    def on_error(self, status):
        print(status)
```     
Here I define a *Tweet listener class* that creates a file called `tweets.txt`, collects streaming tweets and writes them to the file `tweets.txt`.
Once 100 tweets have been streamed, the listener closes the file and stops listening.
Now that we have written the Twitter stream listener class, all you need to do is to create an instance of it and authenticate it.
```python
# Initialize Stream listener
l = MyStreamListener()

# Create your Stream object with authentication
stream = tweepy.Stream(l, auth)
```
You can then stream tweets that contain keywords of choice by applying the `filter` method to the object stream!
```python
# Filter Twitter Streams to capture data by the keywords:
stream.filter(track = ['clinton','trump','sanders','cruz'])
```
Now that you've got your Twitter data sitting locally in a text file, it's time to explore it!
we first begin by reading the Twitter data into a list: `tweets_data`.
```python
# Import package
import json

# String of path to file: tweets_data_path
tweets_data_path = 'tweets.txt'

# Initialize empty list to store tweets: tweets_data
tweets_data = []

# Open connection to file
tweets_file = open(tweets_data_path, "r")

# Read in tweets and store in list: tweets_data
for line in tweets_file:
    tweet = json.loads(line)
    tweets_data.append(tweet)

# Close connection to file
tweets_file.close()
```
Now we have the Twitter data in a list of dictionaries, `tweets_data`, where each dictionary corresponds to a single tweet.
 Next, we're going to extract the text and language of each tweet. The text in a tweet, `t1`, is stored as the value `t1['text']`; similarly, the language is stored in `t1['lang']`. we now build a DataFrame in which each row is a tweet and the columns are `text` and `lang`.
 ```python
 # Import package
import pandas as pd

# Build DataFrame of tweet texts and languages
df = pd.DataFrame(tweets_data, columns=['text','lang'])
```
Now that you have your DataFrame of tweets set up, we're going to do a bit of text analysis to count how many tweets contain the words 'clinton', 'trump', 'sanders' and 'cruz'. To make things simpler, I have defined the following function `word_in_text()`, which will tell you whether the first argument (a word) occurs within the 2nd argument (a tweet).
```python
import re

def word_in_text(word, text):
    word = word.lower()
    text = tweet.lower()
    match = re.search(word, text)

    if match:
        return True
    return False
```
 We're now going to iterate over the rows of the DataFrame and calculate how many tweets contain each of our keywords! The list of objects for each candidate has been initialized to 0.
 ```python
 # Initialize list to store tweet counts
[clinton, trump, sanders, cruz] = [0, 0, 0, 0]

# Iterate through df, counting the number of tweets in which
# each candidate is mentioned
for index, row in df.iterrows():
    clinton += word_in_text('clinton', row['text'])
    trump += word_in_text('trump', row['text'])
    sanders += word_in_text('sanders', row['text'])
    cruz += word_in_text('cruz', row['text'])
```
Now that you have the number of tweets that each candidate was mentioned in, you can plot a bar chart of this data. You'll use the statistical data visualization library `seaborn`.
You'll first `import seaborn as sns`. You'll then construct a *barplot* of the data using `sns.barplot`, passing it two arguments:

* a list of labels
* a list containing the variables you wish to plot (clinton, trump and so on.)
>Hopefully, we'll see that Trump was unreasonably represented!

```python
# Import packages
import matplotlib.pyplot as plt
import seaborn as sns

# Set seaborn style
sns.set(color_codes=True)

# Create a list of labels:cd
cd = ['clinton', 'trump', 'sanders', 'cruz']

# Plot histogram
ax = sns.barplot(cd, [clinton,trump,sanders,cruz])
ax.set(ylabel="count")
plt.show()
```
### BAR PLOT
---
![alt text](https://i.imgur.com/H3DALYC.png "PLOT")
