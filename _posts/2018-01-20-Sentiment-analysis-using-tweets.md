---
layout: post
title: Sentiment Analysis
subtitle: From twitter data 
image: /img/twitter.png
bigimg: /img/twitter.png
---

Sentiment analysis categorizes opinions of users on a given subject into three broad categories - positive, negative and neutral.
These opinions could be expressed through natural langauge or text. In this post, I will analyse emotion or sentiment of textual expressions put out on twitter.
<p> Twitter is a popular platform for people to voice their opinions and can be used for a fairly accurate assesment of public
sentiment on a subject. Twitter provides us with a public API to query their database and these results are the input for my task. </p>

<p> I will be using the public API provided by twitter to query their database. One of the limitations of this API is that it can only get you tweets that are two weeks old or less. Tweets older than that will have to be done using other methods. This restriction limited the topics I could run sentiment analysis on and I had to choose a recent event around the time of this project which was the Golden Globes 2018 award ceremony. One of the highlights of the event was Oprah's speech and how it generated a widely popular sentiment of she running for POTUS. I will be carry out sentiment analysis on Oprah for President to understand if twitter users favor or oppose the idea. </p>

#### Twitter API access
As a first step, I will want access to the twitter API. This can be done by creating a new application on the [Twitter - Developer](https://apps.twitter.com/). This will give me a consumer key, consumer secret code, and access token/secret code, all of which are needed to get access to the API.
<p> </p>
#### Query preparation
<p> Once I am ready to query the database, I need to determine the search terms. For the sentiment in my task, search terms could be "Oprah" and "President"  or "Oprah" and "POTUS" or "Oprah" and "Prez" and so on. To limit the number of tweets to analyse, I will limit my query to "Oprah" and "POTUS". </p>
<p> The event date was Jan 7th, hence I will run the query from Jan 7th upto the present which happens to be Jan 18th.</p>
<p> I will consider original tweets only as the nummber of tweets would be massive if retweets are considered. I would also have to address rate limit issues if retweets are queried. </p> 
I will search for tweets using cursor pagination and a max number of tweets spe
cified. An useful link on [twitter API search](https://stackoverflow.com/questions/22469713/managing-tweepy-api-search/22473254#22473254).
<p> All the tweets queried will be encoded in the UTF8 format. </p> 
<p> </p>
#### Twitter query code
```python

import tweepy

# UTF8 encoding
import sys
reload(sys)
sys.setdefaultencoding('utf8')

# fill in your Twitter credentials 
consumer_key = "abcdef"
consumer_secret = "abcdef"
access_token = "abcdef"
access_token_secret = "abcdef"

# An instance of REST API created
auth = tweepy.AppAuthHandler(consumer_key, consumer_secret)
api = tweepy.API(auth, wait_on_rate_limit=True, wait_on_rate_limit_notify=True)

# Query the database
query = '"Oprah" and "POTUS" -filter:retweets'
max_tweets = 10000
lang = "en"
searched_tweets = [status for status in tweepy.Cursor(api.search, q=query, langauge=lang, since ="2018-01-07").items(max_tweets)]
```
#### Storing the queried data
I will save the tweets searched by the API into a pandas dataframe.

```python
data = pd.DataFrame(data=[tweet.text for tweet in searched_tweets], columns=['Tweets'])
```
#### Feature extraction
All tweets have some methods associated with them that have useful information. These methods are listed below.
```python
['class', 'delattr', 'dict', 'dir', 'doc', 'eq', 'format', 'ge', 'getattribute', 'getstate', 'gt', 'hash', 'init', 'init_subclass', 'le', 'lt', 'module', 'ne', 'new', 'reduce', 'reduce_ex', 'repr', 'setattr', 'sizeof', 'str', 'subclasshook', 'weakref', '_api', '_json', 'author', 'contributors', 'coordinates', 'created_at', 'destroy', 'entities', 'favorite', 'favorite_count', 'favorited', 'geo', 'id', 'id_str', 'in_reply_to_screen_name', 'in_reply_to_status_id', 'in_reply_to_status_id_str', 'in_reply_to_user_id', 'in_reply_to_user_id_str', 'is_quote_status', 'lang', 'parse', 'parse_list', 'place', 'possibly_sensitive', 'retweet', 'retweet_count', 'retweeted', 'retweets', 'source', 'source_url', 'text', 'truncated', 'user']
```
For my analysis, tweet creation date would be interesting. I will use the "created_at" method to extract the date and time of a tweet.
``` python
data['Created On'] = np.array([tweet.created_at for tweet in searched_tweets])
```
Next I will convert the dataframe into a csv file for extracting sentiments.
```python
data.to_csv('tweets.csv')
```



