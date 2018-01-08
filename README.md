
# Distinguishing Sentiments  

## Sentiment Analysis of Media Tweets (01/07/2018)
   1. Visualized summary of the sentiments expressed in Tweets sent out by the following 
      news organizations: BBC, CBS, CNN, Fox, and New York times.
   2. Ranging from -1.0 to 1.0, 
      where a score of 0 expresses a neutral sentiment, 
      -1 the most negative sentiment possible, and 
      +1 the most positive sentiment possible.
      Each plot point is reflected the compound sentiment of a tweet.
   3. Tweet Polarity is split evenly between -1.0 to 1.0 range.
   4. Tweets Ago starting from current 0 to past 100 tweets range

## Overall Media Sentiment based on Twitter (01/07/2018)
    1. Most positive compound sentiments is CBS with 0.19 and 
       Most neutral compund sentiments is BBC with -0.01
    2. CNN and New York Times are same compound sentiments of positive with 0.05
    2. Most negative is Fox media news sentiments with -0.1



```python
# Dependencies
import tweepy
import numpy as np
import json
import time
import random
import requests as req
import datetime
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import csv as cs
plt.style.use('ggplot')
from dateutil import parser
import datetime
```


```python
import yaml
TWITTER_CONFIG_FILE = 'auth.yaml'

with open(TWITTER_CONFIG_FILE, 'r') as config_file:
    config = yaml.load(config_file)
    
print(type(config))
```

    <class 'dict'>



```python
import json

#print(json.dumps(config, indent=4, sort_keys=True))
```


```python
consumer_key = config['twitter']["consumer_key"]
print(consumer_key)
```


```python
# Import and Initialize Sentiment Analyzer
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()

 
```


```python

# Setup Tweepy API Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())

 
```


```python
# Target Search Term
target_terms = ("@BBC", "@CNN", "@CBSNews",
                "@FoxNews", "@nytimes" )

 
```


```python
lang = "en"

# Array to hold sentiments for each tweet
each_sentiments = []

# Array to hold sentiments for each avergage source/target
avg_sentiments = []

 

# Variable for holding the oldest tweet
oldest_tweet = ""

 
```


```python
# Loop through all target news organizations
for target in target_terms:
    
    #Counter
    counter = 1

    # Variables for holding sentiments
    compound_list = []
    positive_list = []
    negative_list = []
    neutral_list = []
 
    # Run search around each tweet and pull 100 tweets
#    public_tweets = api.search(
#        target, count=100, result_type="recent", max_id=oldest_tweet)
    #print("counter %d: " % (counter))

    # Loop through all tweets
    for tweet in public_tweets["statuses"]:
                
        # Use filters to check if user tweet lang is english        
        if tweet["user"]["lang"] == lang:
            if counter == 1:
                print(
                json.dumps(
                tweet,
                sort_keys=True,
                indent=4,
                separators=(',' , ': ')))
                #Print first tweet text
                print("\n")
                print("Source %s: " % (target))
                print("\n")
                print("Tweet %s: %s" % (counter, tweet["text"]))
 

            # Run Vader Analysis on each tweet along with getting info of the tweet created and twitter text
            compound = analyzer.polarity_scores(tweet["text"])["compound"]
            pos = analyzer.polarity_scores(tweet["text"])["pos"]
            neu = analyzer.polarity_scores(tweet["text"])["neu"]
            neg = analyzer.polarity_scores(tweet["text"])["neg"]

            # Add each value to the appropriate array
            compound_list.append(compound)
            positive_list.append(pos)
            negative_list.append(neg)
            neutral_list.append(neu)


            # Add sentiments for each tweet into an array
            each_sentiments.append({"Source": target,
                               "text": tweet["text"],
                               "Date": tweet["created_at"], 
                               "Compound": compound,
                               "Positive": pos,
                               "Negative": neg,
                               "Neutral": neu,
                               "Tweets_Ago": counter})

            # Add to counter 
            counter = counter + 1
            #print("counter %d: " % (counter))
        
        
    # Store the Average Sentiments         
    def color_schema(x):
        col = ""
        if x=="@BBC":
            col = "skyblue"       
        elif x=="@CBSNews":
            col = "green" 
        elif x=="@CNN": 
            col = "red"
        elif x=="@FoxNews":
            col = "blue"
        elif x == "@nytimes":
            col = "yellow"
        return col

    # Store the Average Sentiments      
    avg_sentiments.append({
        "User": target, 
        "color": color_schema(target),
        "Compound": np.mean(compound_list),
        "Positive": np.mean(positive_list),
        "Neutral": np.mean(negative_list),
        "Negative": np.mean(neutral_list),
        "Tweet_Count": len(compound_list)
    })
    # Print the Sentiments
    print(avg_sentiments)
    print("")


    
```

    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Mon Jan 08 05:15:00 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 807095,
                    "id_str": "807095",
                    "indices": [
                        3,
                        11
                    ],
                    "name": "The New York Times",
                    "screen_name": "nytimes"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 950234360288030720,
        "id_str": "950234360288030720",
        "in_reply_to_screen_name": null,
        "in_reply_to_status_id": null,
        "in_reply_to_status_id_str": null,
        "in_reply_to_user_id": null,
        "in_reply_to_user_id_str": null,
        "is_quote_status": false,
        "lang": "en",
        "metadata": {
            "iso_language_code": "en",
            "result_type": "recent"
        },
        "place": null,
        "possibly_sensitive": false,
        "retweet_count": 441,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Mon Jan 08 04:02:02 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/i/web/status/9\u2026",
                        "expanded_url": "https://twitter.com/i/web/status/950215997549678592",
                        "indices": [
                            117,
                            140
                        ],
                        "url": "https://t.co/yUNPt4nLx9"
                    }
                ],
                "user_mentions": []
            },
            "favorite_count": 2238,
            "favorited": false,
            "geo": null,
            "id": 950215997549678592,
            "id_str": "950215997549678592",
            "in_reply_to_screen_name": null,
            "in_reply_to_status_id": null,
            "in_reply_to_status_id_str": null,
            "in_reply_to_user_id": null,
            "in_reply_to_user_id_str": null,
            "is_quote_status": false,
            "lang": "en",
            "metadata": {
                "iso_language_code": "en",
                "result_type": "recent"
            },
            "place": null,
            "possibly_sensitive": false,
            "retweet_count": 441,
            "retweeted": false,
            "source": "<a href=\"http://www.socialflow.com\" rel=\"nofollow\">SocialFlow</a>",
            "text": "Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be pa\u2026 https://t.co/yUNPt4nLx9",
            "truncated": true,
            "user": {
                "contributors_enabled": false,
                "created_at": "Fri Mar 02 20:41:42 +0000 2007",
                "default_profile": false,
                "default_profile_image": false,
                "description": "Where the conversation begins. Follow for breaking news, special reports, RTs of our journalists and more. Visit https://t.co/UVJRxNXe00 to share news tips.",
                "entities": {
                    "description": {
                        "urls": [
                            {
                                "display_url": "nyti.ms/2io2WFI",
                                "expanded_url": "http://nyti.ms/2io2WFI",
                                "indices": [
                                    113,
                                    136
                                ],
                                "url": "https://t.co/UVJRxNXe00"
                            }
                        ]
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "nytimes.com",
                                "expanded_url": "http://www.nytimes.com/",
                                "indices": [
                                    0,
                                    22
                                ],
                                "url": "http://t.co/ahvuWqicF9"
                            }
                        ]
                    }
                },
                "favourites_count": 16052,
                "follow_request_sent": false,
                "followers_count": 40753694,
                "following": false,
                "friends_count": 885,
                "geo_enabled": false,
                "has_extended_profile": false,
                "id": 807095,
                "id_str": "807095",
                "is_translation_enabled": true,
                "is_translator": false,
                "lang": "en",
                "listed_count": 193823,
                "location": "New York City",
                "name": "The New York Times",
                "notifications": false,
                "profile_background_color": "131516",
                "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_tile": true,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/807095/1513258368",
                "profile_image_url": "http://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_link_color": "607696",
                "profile_sidebar_border_color": "FFFFFF",
                "profile_sidebar_fill_color": "EFEFEF",
                "profile_text_color": "333333",
                "profile_use_background_image": true,
                "protected": false,
                "screen_name": "nytimes",
                "statuses_count": 305250,
                "time_zone": "Eastern Time (US & Canada)",
                "translator_type": "none",
                "url": "http://t.co/ahvuWqicF9",
                "utc_offset": -18000,
                "verified": true
            }
        },
        "source": "<a href=\"http://twitter.com/#!/download/ipad\" rel=\"nofollow\">Twitter for iPad</a>",
        "text": "RT @nytimes: Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be part of the h\u2026",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Tue May 26 09:58:32 +0000 2009",
            "default_profile": true,
            "default_profile_image": false,
            "description": "Bordering on feral. And not in the good way.",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 127837,
            "follow_request_sent": false,
            "followers_count": 1470,
            "following": false,
            "friends_count": 1790,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 42597178,
            "id_str": "42597178",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 54,
            "location": "The Land of Elton John's Hubby",
            "name": "AxeGrrl",
            "notifications": false,
            "profile_background_color": "C0DEED",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_tile": false,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/42597178/1486380864",
            "profile_image_url": "http://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_link_color": "1DA1F2",
            "profile_sidebar_border_color": "C0DEED",
            "profile_sidebar_fill_color": "DDEEF6",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "Axe_Grrl",
            "statuses_count": 54175,
            "time_zone": "Quito",
            "translator_type": "none",
            "url": null,
            "utc_offset": -18000,
            "verified": false
        }
    }
    
    
    Source @BBC: 
    
    
    Tweet 1: RT @nytimes: Frances McDormand, in 2017: "I’m not an actor because I want my picture taken. I’m an actor because I want to be part of the h…
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': -0.0061493827160493814, 'Positive': 0.079098765432098761, 'Neutral': 0.087160493827160485, 'Negative': 0.83375308641975321, 'Tweet_Count': 81}, {'User': '@CNN', 'color': 'red', 'Compound': 0.18816170212765954, 'Positive': 0.10374468085106382, 'Neutral': 0.050308510638297867, 'Negative': 0.84596808510638311, 'Tweet_Count': 94}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.047965624999999991, 'Positive': 0.075145833333333342, 'Neutral': 0.066468750000000007, 'Negative': 0.85834375000000007, 'Tweet_Count': 96}, {'User': '@FoxNews', 'color': 'blue', 'Compound': -0.1034295918367347, 'Positive': 0.062775510204081633, 'Neutral': 0.1154081632653061, 'Negative': 0.82177551020408157, 'Tweet_Count': 98}, {'User': '@nytimes', 'color': 'yellow', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@BBC', 'color': 'skyblue', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Mon Jan 08 05:15:00 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 807095,
                    "id_str": "807095",
                    "indices": [
                        3,
                        11
                    ],
                    "name": "The New York Times",
                    "screen_name": "nytimes"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 950234360288030720,
        "id_str": "950234360288030720",
        "in_reply_to_screen_name": null,
        "in_reply_to_status_id": null,
        "in_reply_to_status_id_str": null,
        "in_reply_to_user_id": null,
        "in_reply_to_user_id_str": null,
        "is_quote_status": false,
        "lang": "en",
        "metadata": {
            "iso_language_code": "en",
            "result_type": "recent"
        },
        "place": null,
        "possibly_sensitive": false,
        "retweet_count": 441,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Mon Jan 08 04:02:02 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/i/web/status/9\u2026",
                        "expanded_url": "https://twitter.com/i/web/status/950215997549678592",
                        "indices": [
                            117,
                            140
                        ],
                        "url": "https://t.co/yUNPt4nLx9"
                    }
                ],
                "user_mentions": []
            },
            "favorite_count": 2238,
            "favorited": false,
            "geo": null,
            "id": 950215997549678592,
            "id_str": "950215997549678592",
            "in_reply_to_screen_name": null,
            "in_reply_to_status_id": null,
            "in_reply_to_status_id_str": null,
            "in_reply_to_user_id": null,
            "in_reply_to_user_id_str": null,
            "is_quote_status": false,
            "lang": "en",
            "metadata": {
                "iso_language_code": "en",
                "result_type": "recent"
            },
            "place": null,
            "possibly_sensitive": false,
            "retweet_count": 441,
            "retweeted": false,
            "source": "<a href=\"http://www.socialflow.com\" rel=\"nofollow\">SocialFlow</a>",
            "text": "Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be pa\u2026 https://t.co/yUNPt4nLx9",
            "truncated": true,
            "user": {
                "contributors_enabled": false,
                "created_at": "Fri Mar 02 20:41:42 +0000 2007",
                "default_profile": false,
                "default_profile_image": false,
                "description": "Where the conversation begins. Follow for breaking news, special reports, RTs of our journalists and more. Visit https://t.co/UVJRxNXe00 to share news tips.",
                "entities": {
                    "description": {
                        "urls": [
                            {
                                "display_url": "nyti.ms/2io2WFI",
                                "expanded_url": "http://nyti.ms/2io2WFI",
                                "indices": [
                                    113,
                                    136
                                ],
                                "url": "https://t.co/UVJRxNXe00"
                            }
                        ]
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "nytimes.com",
                                "expanded_url": "http://www.nytimes.com/",
                                "indices": [
                                    0,
                                    22
                                ],
                                "url": "http://t.co/ahvuWqicF9"
                            }
                        ]
                    }
                },
                "favourites_count": 16052,
                "follow_request_sent": false,
                "followers_count": 40753694,
                "following": false,
                "friends_count": 885,
                "geo_enabled": false,
                "has_extended_profile": false,
                "id": 807095,
                "id_str": "807095",
                "is_translation_enabled": true,
                "is_translator": false,
                "lang": "en",
                "listed_count": 193823,
                "location": "New York City",
                "name": "The New York Times",
                "notifications": false,
                "profile_background_color": "131516",
                "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_tile": true,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/807095/1513258368",
                "profile_image_url": "http://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_link_color": "607696",
                "profile_sidebar_border_color": "FFFFFF",
                "profile_sidebar_fill_color": "EFEFEF",
                "profile_text_color": "333333",
                "profile_use_background_image": true,
                "protected": false,
                "screen_name": "nytimes",
                "statuses_count": 305250,
                "time_zone": "Eastern Time (US & Canada)",
                "translator_type": "none",
                "url": "http://t.co/ahvuWqicF9",
                "utc_offset": -18000,
                "verified": true
            }
        },
        "source": "<a href=\"http://twitter.com/#!/download/ipad\" rel=\"nofollow\">Twitter for iPad</a>",
        "text": "RT @nytimes: Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be part of the h\u2026",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Tue May 26 09:58:32 +0000 2009",
            "default_profile": true,
            "default_profile_image": false,
            "description": "Bordering on feral. And not in the good way.",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 127837,
            "follow_request_sent": false,
            "followers_count": 1470,
            "following": false,
            "friends_count": 1790,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 42597178,
            "id_str": "42597178",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 54,
            "location": "The Land of Elton John's Hubby",
            "name": "AxeGrrl",
            "notifications": false,
            "profile_background_color": "C0DEED",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_tile": false,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/42597178/1486380864",
            "profile_image_url": "http://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_link_color": "1DA1F2",
            "profile_sidebar_border_color": "C0DEED",
            "profile_sidebar_fill_color": "DDEEF6",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "Axe_Grrl",
            "statuses_count": 54175,
            "time_zone": "Quito",
            "translator_type": "none",
            "url": null,
            "utc_offset": -18000,
            "verified": false
        }
    }
    
    
    Source @CNN: 
    
    
    Tweet 1: RT @nytimes: Frances McDormand, in 2017: "I’m not an actor because I want my picture taken. I’m an actor because I want to be part of the h…
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': -0.0061493827160493814, 'Positive': 0.079098765432098761, 'Neutral': 0.087160493827160485, 'Negative': 0.83375308641975321, 'Tweet_Count': 81}, {'User': '@CNN', 'color': 'red', 'Compound': 0.18816170212765954, 'Positive': 0.10374468085106382, 'Neutral': 0.050308510638297867, 'Negative': 0.84596808510638311, 'Tweet_Count': 94}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.047965624999999991, 'Positive': 0.075145833333333342, 'Neutral': 0.066468750000000007, 'Negative': 0.85834375000000007, 'Tweet_Count': 96}, {'User': '@FoxNews', 'color': 'blue', 'Compound': -0.1034295918367347, 'Positive': 0.062775510204081633, 'Neutral': 0.1154081632653061, 'Negative': 0.82177551020408157, 'Tweet_Count': 98}, {'User': '@nytimes', 'color': 'yellow', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@BBC', 'color': 'skyblue', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@CNN', 'color': 'red', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Mon Jan 08 05:15:00 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 807095,
                    "id_str": "807095",
                    "indices": [
                        3,
                        11
                    ],
                    "name": "The New York Times",
                    "screen_name": "nytimes"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 950234360288030720,
        "id_str": "950234360288030720",
        "in_reply_to_screen_name": null,
        "in_reply_to_status_id": null,
        "in_reply_to_status_id_str": null,
        "in_reply_to_user_id": null,
        "in_reply_to_user_id_str": null,
        "is_quote_status": false,
        "lang": "en",
        "metadata": {
            "iso_language_code": "en",
            "result_type": "recent"
        },
        "place": null,
        "possibly_sensitive": false,
        "retweet_count": 441,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Mon Jan 08 04:02:02 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/i/web/status/9\u2026",
                        "expanded_url": "https://twitter.com/i/web/status/950215997549678592",
                        "indices": [
                            117,
                            140
                        ],
                        "url": "https://t.co/yUNPt4nLx9"
                    }
                ],
                "user_mentions": []
            },
            "favorite_count": 2238,
            "favorited": false,
            "geo": null,
            "id": 950215997549678592,
            "id_str": "950215997549678592",
            "in_reply_to_screen_name": null,
            "in_reply_to_status_id": null,
            "in_reply_to_status_id_str": null,
            "in_reply_to_user_id": null,
            "in_reply_to_user_id_str": null,
            "is_quote_status": false,
            "lang": "en",
            "metadata": {
                "iso_language_code": "en",
                "result_type": "recent"
            },
            "place": null,
            "possibly_sensitive": false,
            "retweet_count": 441,
            "retweeted": false,
            "source": "<a href=\"http://www.socialflow.com\" rel=\"nofollow\">SocialFlow</a>",
            "text": "Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be pa\u2026 https://t.co/yUNPt4nLx9",
            "truncated": true,
            "user": {
                "contributors_enabled": false,
                "created_at": "Fri Mar 02 20:41:42 +0000 2007",
                "default_profile": false,
                "default_profile_image": false,
                "description": "Where the conversation begins. Follow for breaking news, special reports, RTs of our journalists and more. Visit https://t.co/UVJRxNXe00 to share news tips.",
                "entities": {
                    "description": {
                        "urls": [
                            {
                                "display_url": "nyti.ms/2io2WFI",
                                "expanded_url": "http://nyti.ms/2io2WFI",
                                "indices": [
                                    113,
                                    136
                                ],
                                "url": "https://t.co/UVJRxNXe00"
                            }
                        ]
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "nytimes.com",
                                "expanded_url": "http://www.nytimes.com/",
                                "indices": [
                                    0,
                                    22
                                ],
                                "url": "http://t.co/ahvuWqicF9"
                            }
                        ]
                    }
                },
                "favourites_count": 16052,
                "follow_request_sent": false,
                "followers_count": 40753694,
                "following": false,
                "friends_count": 885,
                "geo_enabled": false,
                "has_extended_profile": false,
                "id": 807095,
                "id_str": "807095",
                "is_translation_enabled": true,
                "is_translator": false,
                "lang": "en",
                "listed_count": 193823,
                "location": "New York City",
                "name": "The New York Times",
                "notifications": false,
                "profile_background_color": "131516",
                "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_tile": true,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/807095/1513258368",
                "profile_image_url": "http://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_link_color": "607696",
                "profile_sidebar_border_color": "FFFFFF",
                "profile_sidebar_fill_color": "EFEFEF",
                "profile_text_color": "333333",
                "profile_use_background_image": true,
                "protected": false,
                "screen_name": "nytimes",
                "statuses_count": 305250,
                "time_zone": "Eastern Time (US & Canada)",
                "translator_type": "none",
                "url": "http://t.co/ahvuWqicF9",
                "utc_offset": -18000,
                "verified": true
            }
        },
        "source": "<a href=\"http://twitter.com/#!/download/ipad\" rel=\"nofollow\">Twitter for iPad</a>",
        "text": "RT @nytimes: Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be part of the h\u2026",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Tue May 26 09:58:32 +0000 2009",
            "default_profile": true,
            "default_profile_image": false,
            "description": "Bordering on feral. And not in the good way.",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 127837,
            "follow_request_sent": false,
            "followers_count": 1470,
            "following": false,
            "friends_count": 1790,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 42597178,
            "id_str": "42597178",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 54,
            "location": "The Land of Elton John's Hubby",
            "name": "AxeGrrl",
            "notifications": false,
            "profile_background_color": "C0DEED",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_tile": false,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/42597178/1486380864",
            "profile_image_url": "http://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_link_color": "1DA1F2",
            "profile_sidebar_border_color": "C0DEED",
            "profile_sidebar_fill_color": "DDEEF6",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "Axe_Grrl",
            "statuses_count": 54175,
            "time_zone": "Quito",
            "translator_type": "none",
            "url": null,
            "utc_offset": -18000,
            "verified": false
        }
    }
    
    
    Source @CBSNews: 
    
    
    Tweet 1: RT @nytimes: Frances McDormand, in 2017: "I’m not an actor because I want my picture taken. I’m an actor because I want to be part of the h…
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': -0.0061493827160493814, 'Positive': 0.079098765432098761, 'Neutral': 0.087160493827160485, 'Negative': 0.83375308641975321, 'Tweet_Count': 81}, {'User': '@CNN', 'color': 'red', 'Compound': 0.18816170212765954, 'Positive': 0.10374468085106382, 'Neutral': 0.050308510638297867, 'Negative': 0.84596808510638311, 'Tweet_Count': 94}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.047965624999999991, 'Positive': 0.075145833333333342, 'Neutral': 0.066468750000000007, 'Negative': 0.85834375000000007, 'Tweet_Count': 96}, {'User': '@FoxNews', 'color': 'blue', 'Compound': -0.1034295918367347, 'Positive': 0.062775510204081633, 'Neutral': 0.1154081632653061, 'Negative': 0.82177551020408157, 'Tweet_Count': 98}, {'User': '@nytimes', 'color': 'yellow', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@BBC', 'color': 'skyblue', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@CNN', 'color': 'red', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Mon Jan 08 05:15:00 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 807095,
                    "id_str": "807095",
                    "indices": [
                        3,
                        11
                    ],
                    "name": "The New York Times",
                    "screen_name": "nytimes"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 950234360288030720,
        "id_str": "950234360288030720",
        "in_reply_to_screen_name": null,
        "in_reply_to_status_id": null,
        "in_reply_to_status_id_str": null,
        "in_reply_to_user_id": null,
        "in_reply_to_user_id_str": null,
        "is_quote_status": false,
        "lang": "en",
        "metadata": {
            "iso_language_code": "en",
            "result_type": "recent"
        },
        "place": null,
        "possibly_sensitive": false,
        "retweet_count": 441,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Mon Jan 08 04:02:02 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/i/web/status/9\u2026",
                        "expanded_url": "https://twitter.com/i/web/status/950215997549678592",
                        "indices": [
                            117,
                            140
                        ],
                        "url": "https://t.co/yUNPt4nLx9"
                    }
                ],
                "user_mentions": []
            },
            "favorite_count": 2238,
            "favorited": false,
            "geo": null,
            "id": 950215997549678592,
            "id_str": "950215997549678592",
            "in_reply_to_screen_name": null,
            "in_reply_to_status_id": null,
            "in_reply_to_status_id_str": null,
            "in_reply_to_user_id": null,
            "in_reply_to_user_id_str": null,
            "is_quote_status": false,
            "lang": "en",
            "metadata": {
                "iso_language_code": "en",
                "result_type": "recent"
            },
            "place": null,
            "possibly_sensitive": false,
            "retweet_count": 441,
            "retweeted": false,
            "source": "<a href=\"http://www.socialflow.com\" rel=\"nofollow\">SocialFlow</a>",
            "text": "Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be pa\u2026 https://t.co/yUNPt4nLx9",
            "truncated": true,
            "user": {
                "contributors_enabled": false,
                "created_at": "Fri Mar 02 20:41:42 +0000 2007",
                "default_profile": false,
                "default_profile_image": false,
                "description": "Where the conversation begins. Follow for breaking news, special reports, RTs of our journalists and more. Visit https://t.co/UVJRxNXe00 to share news tips.",
                "entities": {
                    "description": {
                        "urls": [
                            {
                                "display_url": "nyti.ms/2io2WFI",
                                "expanded_url": "http://nyti.ms/2io2WFI",
                                "indices": [
                                    113,
                                    136
                                ],
                                "url": "https://t.co/UVJRxNXe00"
                            }
                        ]
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "nytimes.com",
                                "expanded_url": "http://www.nytimes.com/",
                                "indices": [
                                    0,
                                    22
                                ],
                                "url": "http://t.co/ahvuWqicF9"
                            }
                        ]
                    }
                },
                "favourites_count": 16052,
                "follow_request_sent": false,
                "followers_count": 40753694,
                "following": false,
                "friends_count": 885,
                "geo_enabled": false,
                "has_extended_profile": false,
                "id": 807095,
                "id_str": "807095",
                "is_translation_enabled": true,
                "is_translator": false,
                "lang": "en",
                "listed_count": 193823,
                "location": "New York City",
                "name": "The New York Times",
                "notifications": false,
                "profile_background_color": "131516",
                "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_tile": true,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/807095/1513258368",
                "profile_image_url": "http://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_link_color": "607696",
                "profile_sidebar_border_color": "FFFFFF",
                "profile_sidebar_fill_color": "EFEFEF",
                "profile_text_color": "333333",
                "profile_use_background_image": true,
                "protected": false,
                "screen_name": "nytimes",
                "statuses_count": 305250,
                "time_zone": "Eastern Time (US & Canada)",
                "translator_type": "none",
                "url": "http://t.co/ahvuWqicF9",
                "utc_offset": -18000,
                "verified": true
            }
        },
        "source": "<a href=\"http://twitter.com/#!/download/ipad\" rel=\"nofollow\">Twitter for iPad</a>",
        "text": "RT @nytimes: Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be part of the h\u2026",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Tue May 26 09:58:32 +0000 2009",
            "default_profile": true,
            "default_profile_image": false,
            "description": "Bordering on feral. And not in the good way.",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 127837,
            "follow_request_sent": false,
            "followers_count": 1470,
            "following": false,
            "friends_count": 1790,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 42597178,
            "id_str": "42597178",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 54,
            "location": "The Land of Elton John's Hubby",
            "name": "AxeGrrl",
            "notifications": false,
            "profile_background_color": "C0DEED",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_tile": false,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/42597178/1486380864",
            "profile_image_url": "http://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_link_color": "1DA1F2",
            "profile_sidebar_border_color": "C0DEED",
            "profile_sidebar_fill_color": "DDEEF6",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "Axe_Grrl",
            "statuses_count": 54175,
            "time_zone": "Quito",
            "translator_type": "none",
            "url": null,
            "utc_offset": -18000,
            "verified": false
        }
    }
    
    
    Source @FoxNews: 
    
    
    Tweet 1: RT @nytimes: Frances McDormand, in 2017: "I’m not an actor because I want my picture taken. I’m an actor because I want to be part of the h…
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': -0.0061493827160493814, 'Positive': 0.079098765432098761, 'Neutral': 0.087160493827160485, 'Negative': 0.83375308641975321, 'Tweet_Count': 81}, {'User': '@CNN', 'color': 'red', 'Compound': 0.18816170212765954, 'Positive': 0.10374468085106382, 'Neutral': 0.050308510638297867, 'Negative': 0.84596808510638311, 'Tweet_Count': 94}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.047965624999999991, 'Positive': 0.075145833333333342, 'Neutral': 0.066468750000000007, 'Negative': 0.85834375000000007, 'Tweet_Count': 96}, {'User': '@FoxNews', 'color': 'blue', 'Compound': -0.1034295918367347, 'Positive': 0.062775510204081633, 'Neutral': 0.1154081632653061, 'Negative': 0.82177551020408157, 'Tweet_Count': 98}, {'User': '@nytimes', 'color': 'yellow', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@BBC', 'color': 'skyblue', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@CNN', 'color': 'red', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@FoxNews', 'color': 'blue', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Mon Jan 08 05:15:00 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 807095,
                    "id_str": "807095",
                    "indices": [
                        3,
                        11
                    ],
                    "name": "The New York Times",
                    "screen_name": "nytimes"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 950234360288030720,
        "id_str": "950234360288030720",
        "in_reply_to_screen_name": null,
        "in_reply_to_status_id": null,
        "in_reply_to_status_id_str": null,
        "in_reply_to_user_id": null,
        "in_reply_to_user_id_str": null,
        "is_quote_status": false,
        "lang": "en",
        "metadata": {
            "iso_language_code": "en",
            "result_type": "recent"
        },
        "place": null,
        "possibly_sensitive": false,
        "retweet_count": 441,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Mon Jan 08 04:02:02 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/i/web/status/9\u2026",
                        "expanded_url": "https://twitter.com/i/web/status/950215997549678592",
                        "indices": [
                            117,
                            140
                        ],
                        "url": "https://t.co/yUNPt4nLx9"
                    }
                ],
                "user_mentions": []
            },
            "favorite_count": 2238,
            "favorited": false,
            "geo": null,
            "id": 950215997549678592,
            "id_str": "950215997549678592",
            "in_reply_to_screen_name": null,
            "in_reply_to_status_id": null,
            "in_reply_to_status_id_str": null,
            "in_reply_to_user_id": null,
            "in_reply_to_user_id_str": null,
            "is_quote_status": false,
            "lang": "en",
            "metadata": {
                "iso_language_code": "en",
                "result_type": "recent"
            },
            "place": null,
            "possibly_sensitive": false,
            "retweet_count": 441,
            "retweeted": false,
            "source": "<a href=\"http://www.socialflow.com\" rel=\"nofollow\">SocialFlow</a>",
            "text": "Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be pa\u2026 https://t.co/yUNPt4nLx9",
            "truncated": true,
            "user": {
                "contributors_enabled": false,
                "created_at": "Fri Mar 02 20:41:42 +0000 2007",
                "default_profile": false,
                "default_profile_image": false,
                "description": "Where the conversation begins. Follow for breaking news, special reports, RTs of our journalists and more. Visit https://t.co/UVJRxNXe00 to share news tips.",
                "entities": {
                    "description": {
                        "urls": [
                            {
                                "display_url": "nyti.ms/2io2WFI",
                                "expanded_url": "http://nyti.ms/2io2WFI",
                                "indices": [
                                    113,
                                    136
                                ],
                                "url": "https://t.co/UVJRxNXe00"
                            }
                        ]
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "nytimes.com",
                                "expanded_url": "http://www.nytimes.com/",
                                "indices": [
                                    0,
                                    22
                                ],
                                "url": "http://t.co/ahvuWqicF9"
                            }
                        ]
                    }
                },
                "favourites_count": 16052,
                "follow_request_sent": false,
                "followers_count": 40753694,
                "following": false,
                "friends_count": 885,
                "geo_enabled": false,
                "has_extended_profile": false,
                "id": 807095,
                "id_str": "807095",
                "is_translation_enabled": true,
                "is_translator": false,
                "lang": "en",
                "listed_count": 193823,
                "location": "New York City",
                "name": "The New York Times",
                "notifications": false,
                "profile_background_color": "131516",
                "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/736339684/948f072cc2da4e3a5e9f2ebfb3b1a0e7.png",
                "profile_background_tile": true,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/807095/1513258368",
                "profile_image_url": "http://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/942784892882112513/qV4xB0I3_normal.jpg",
                "profile_link_color": "607696",
                "profile_sidebar_border_color": "FFFFFF",
                "profile_sidebar_fill_color": "EFEFEF",
                "profile_text_color": "333333",
                "profile_use_background_image": true,
                "protected": false,
                "screen_name": "nytimes",
                "statuses_count": 305250,
                "time_zone": "Eastern Time (US & Canada)",
                "translator_type": "none",
                "url": "http://t.co/ahvuWqicF9",
                "utc_offset": -18000,
                "verified": true
            }
        },
        "source": "<a href=\"http://twitter.com/#!/download/ipad\" rel=\"nofollow\">Twitter for iPad</a>",
        "text": "RT @nytimes: Frances McDormand, in 2017: \"I\u2019m not an actor because I want my picture taken. I\u2019m an actor because I want to be part of the h\u2026",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Tue May 26 09:58:32 +0000 2009",
            "default_profile": true,
            "default_profile_image": false,
            "description": "Bordering on feral. And not in the good way.",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 127837,
            "follow_request_sent": false,
            "followers_count": 1470,
            "following": false,
            "friends_count": 1790,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 42597178,
            "id_str": "42597178",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 54,
            "location": "The Land of Elton John's Hubby",
            "name": "AxeGrrl",
            "notifications": false,
            "profile_background_color": "C0DEED",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_tile": false,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/42597178/1486380864",
            "profile_image_url": "http://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/947289198750502912/gqSzILAF_normal.jpg",
            "profile_link_color": "1DA1F2",
            "profile_sidebar_border_color": "C0DEED",
            "profile_sidebar_fill_color": "DDEEF6",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "Axe_Grrl",
            "statuses_count": 54175,
            "time_zone": "Quito",
            "translator_type": "none",
            "url": null,
            "utc_offset": -18000,
            "verified": false
        }
    }
    
    
    Source @nytimes: 
    
    
    Tweet 1: RT @nytimes: Frances McDormand, in 2017: "I’m not an actor because I want my picture taken. I’m an actor because I want to be part of the h…
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': -0.0061493827160493814, 'Positive': 0.079098765432098761, 'Neutral': 0.087160493827160485, 'Negative': 0.83375308641975321, 'Tweet_Count': 81}, {'User': '@CNN', 'color': 'red', 'Compound': 0.18816170212765954, 'Positive': 0.10374468085106382, 'Neutral': 0.050308510638297867, 'Negative': 0.84596808510638311, 'Tweet_Count': 94}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.047965624999999991, 'Positive': 0.075145833333333342, 'Neutral': 0.066468750000000007, 'Negative': 0.85834375000000007, 'Tweet_Count': 96}, {'User': '@FoxNews', 'color': 'blue', 'Compound': -0.1034295918367347, 'Positive': 0.062775510204081633, 'Neutral': 0.1154081632653061, 'Negative': 0.82177551020408157, 'Tweet_Count': 98}, {'User': '@nytimes', 'color': 'yellow', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@BBC', 'color': 'skyblue', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@CNN', 'color': 'red', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@FoxNews', 'color': 'blue', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}, {'User': '@nytimes', 'color': 'yellow', 'Compound': 0.048796739130434787, 'Positive': 0.06420652173913044, 'Neutral': 0.030554347826086955, 'Negative': 0.90521739130434786, 'Tweet_Count': 92}]
    



```python
# Print the Sentiments
#print(each_sentiments)
#print("")
```


```python
# Convert each sentiments to DataFrame
each_sentiments_pd = pd.DataFrame.from_dict(each_sentiments)
each_sentiments_pd.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Compound</th>
      <th>Date</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Source</th>
      <th>Tweets_Ago</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0000</td>
      <td>Mon Jan 08 03:36:51 +0000 2018</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.0</td>
      <td>@BBC</td>
      <td>1</td>
      <td>RT @fafazh124: Nonton biar cerdas @VIVAcoid @m...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.2023</td>
      <td>Mon Jan 08 03:36:26 +0000 2018</td>
      <td>0.101</td>
      <td>0.899</td>
      <td>0.0</td>
      <td>@BBC</td>
      <td>2</td>
      <td>🚨NOTICE🚨\n\n@POTUS @realDonaldTrump's\n1st Ann...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0000</td>
      <td>Mon Jan 08 03:34:00 +0000 2018</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.0</td>
      <td>@BBC</td>
      <td>3</td>
      <td>@FoxNews @DailyMailUK @BBC @BBCWorld @MailOnli...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.4588</td>
      <td>Mon Jan 08 03:33:59 +0000 2018</td>
      <td>0.333</td>
      <td>0.667</td>
      <td>0.0</td>
      <td>@BBC</td>
      <td>4</td>
      <td>@KattyKayBBC @BBCCarrie @BBC this is unaccepta...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0000</td>
      <td>Mon Jan 08 03:33:59 +0000 2018</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.0</td>
      <td>@BBC</td>
      <td>5</td>
      <td>RT @ProfElvanAktas: "Kanlı Tiyatro"\n"Deadly T...</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Saving all my extracted data to the csv file 
each_sentiments_pd.to_csv('./extracted_tweets_each_sentiments_pd.csv')
```


```python
#Convert each source average sentiments to DataFrame
avg_sentiments_pd = pd.DataFrame.from_dict(avg_sentiments)
avg_sentiments_pd

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweet_Count</th>
      <th>User</th>
      <th>color</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.047487</td>
      <td>0.855388</td>
      <td>0.082024</td>
      <td>0.062600</td>
      <td>85</td>
      <td>@BBC</td>
      <td>skyblue</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.005042</td>
      <td>0.844391</td>
      <td>0.070489</td>
      <td>0.085130</td>
      <td>92</td>
      <td>@CNN</td>
      <td>red</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.137368</td>
      <td>0.873556</td>
      <td>0.033678</td>
      <td>0.092867</td>
      <td>90</td>
      <td>@CBSNews</td>
      <td>green</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.040004</td>
      <td>0.846680</td>
      <td>0.081470</td>
      <td>0.071830</td>
      <td>100</td>
      <td>@FoxNews</td>
      <td>blue</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.001232</td>
      <td>0.902010</td>
      <td>0.048485</td>
      <td>0.049464</td>
      <td>97</td>
      <td>@nytimes</td>
      <td>yellow</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Saving all my extracted data to the csv file 
avg_sentiments_pd.to_csv('./extracted_average_source_sentiments_pd.csv')
```


```python
# obtain the x and y coordinates for each 100 tweets of the 5 Media Sources
BBC = each_sentiments_pd[each_sentiments_pd["Source"] == "@BBC"]
CBS = each_sentiments_pd[each_sentiments_pd["Source"] == "@CBSNews"] 
CNN = each_sentiments_pd[each_sentiments_pd["Source"] == "@CNN"] 
Fox = each_sentiments_pd[each_sentiments_pd["Source"] == "@FoxNews"] 
nytimes = each_sentiments_pd[each_sentiments_pd["Source"] == "@nytimes"] 
#print(BBC) 
```


```python
# Build the scatter plots for each Media source 
plt.scatter(BBC["Tweets_Ago"], 
            BBC["Compound"], 
            s=100, c="skyblue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="BBC")

plt.scatter(CBS["Tweets_Ago"], 
            CBS["Compound"], 
            s=100, c="green", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="CBS")

plt.scatter(CNN["Tweets_Ago"], 
            CNN["Compound"], 
            s=100, c="red", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="CNN")

plt.scatter(Fox["Tweets_Ago"], 
            Fox["Compound"], 
            s=100, c="blue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Fox")

plt.scatter(nytimes["Tweets_Ago"], 
            nytimes["Compound"], 
            s=100, c="yellow", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="New York Times")

# Incorporate the other graph properties
plt.title("Sentiment Analysis of Media Tweets (01/07/18)")
plt.ylabel("Tweet Polarity")
plt.xlabel("Tweets Ago")
plt.xlim((100, -5))
plt.ylim((-1.125, 1.125))
plt.grid(True)

# Create a legend
plt.legend(bbox_to_anchor=(1.04,1), loc="upper left", title="Media Sources")

# Save Figure
plt.savefig("sentiment_analysis_of_media_tweets.png")

# Show plot
plt.show()
 
```


![png](Twitter-Social_Analytics_files/Twitter-Social_Analytics_16_0.png)



```python
# Obtain the x and y coordinates for each average compound sentiments for 5 media Sources
BBC_media = avg_sentiments_pd[avg_sentiments_pd["User"] == "@BBC"] 
CBS_media = avg_sentiments_pd[avg_sentiments_pd["User"] == "@CBSNews"]
CNN_media = avg_sentiments_pd[avg_sentiments_pd["User"] == "@CNN"]
Fox_media = avg_sentiments_pd[avg_sentiments_pd["User"] == "@FoxNews"]
nytimes_media = avg_sentiments_pd[avg_sentiments_pd["User"] == "@nytimes"]
#print(BBC_media)
#len(avg_sentiments_pd)
avg_sentiments_pd.iloc[0].Compound
ind = np.arange(len(avg_sentiments_pd))
print(ind[0])
avg_sentiments_pd.iloc[0].User
print(BBC_media)
```

    0
       Compound  Negative  Neutral  Positive  Tweet_Count  User    color
    0 -0.006149  0.833753  0.08716  0.079099           81  @BBC  skyblue



```python
# Slice the data between the 5 social overall media sentiments of Compound
fig, ax = plt.subplots()
ind = np.arange(len(avg_sentiments_pd))  
width = 1
rectsBBC = ax.bar(ind[0], avg_sentiments_pd.iloc[0].Compound, width, color='skyblue')
rectsCNN = ax.bar(ind[1], avg_sentiments_pd.iloc[1].Compound, width, color='red')
rectsCBS = ax.bar(ind[2], avg_sentiments_pd.iloc[2].Compound, width, color='green')
rectsFox = ax.bar(ind[3], avg_sentiments_pd.iloc[3].Compound, width, color='blue')
rectsnytimes = ax.bar(ind[4], avg_sentiments_pd.iloc[4].Compound, width, color='yellow')

# Orient widths. Add labels, tick marks, etc. 
ax.set_ylabel('Tweet Polarity')
ax.set_title('Overall Media Sentiment based on Twitter (01/07/18)')
ax.set_xticks(ind + 0.5)
ax.set_xticklabels(('BBC', 'CBS', 'CNN', 'Fox', 'NYT'))
ax.set_autoscaley_on(False)
ax.set_ylim([-0.125,0.25])
ax.grid(True)

# Use functions to label the negative and positive of sentiment changes
def autolabel(rects):
    for rect in rects:
        height = rect.get_height()
        height = round(height, 2) 
        print(height)
        ax.text(rect.get_x() + rect.get_width()/2., height,
                '%s' % (str(float(height))),
                ha='center', va='bottom', color="black", fontsize="9")

# Call functions to implement the function calls
autolabel(rectsBBC)
autolabel(rectsCBS)
autolabel(rectsCNN)
autolabel(rectsFox)     
autolabel(rectsnytimes)

# Save the Figure
fig.savefig("Overall_media_sentiment_based_on_twitter.png")

# Show the Figure
fig.show() 
```

    -0.01
    0.05
    0.19
    -0.1
    0.05


    /Users/sunitharamakrishnan/anaconda3/envs/PythonData/lib/python3.6/site-packages/matplotlib/figure.py:418: UserWarning: matplotlib is currently using a non-GUI backend, so cannot show the figure
      "matplotlib is currently using a non-GUI backend, "


# ![image.png](attachment:image.png)


```python
 
```
