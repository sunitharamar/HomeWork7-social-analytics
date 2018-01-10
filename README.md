
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
      ( I am achieving 100 tweets goal by starting with current tweet and searching 
        through 2 pages of tweets. 2 * 100 = 200 tweets. With "counter" variable , I break once I reach 100 tweets.
        Because when I was searching through only 1 page, I was always getting less than 100 tweets)

## Overall Media Sentiment based on Twitter (01/10/2018)
    1. Most positive compound sentiments is CNN with 0.05  
    2. Almost~ Neutral is BBC & New York Times media news sentiments with -0.01 & 0.01
    2. Most negative is CBS media news sentiments with -0.1



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
import csv as csl
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
    
    
    # Loop through each page (total of 100 tweets, 1 page = 100 )
    #for x in range(2): # loop through 2 page. max of each page is 100 tweets 100 * 2 = 200 tweets
    
    for x in range(2): # loop through 2 page. max of each page is 100 tweets
        
        # Run search around each tweet and pull 100 tweets
        public_tweets = api.search(
            target, count=100, result_type="recent", max_id=oldest_tweet)
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
                if counter == 101: # once the tweets reach 100, then we break the loop
                    break 
                    print("Achieved goal of counter %d: " % (counter))


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
        "created_at": "Wed Jan 10 16:10:43 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [
                {
                    "display_url": "twitter.com/bbcnews/status\u2026",
                    "expanded_url": "https://twitter.com/bbcnews/status/950764582284521474",
                    "indices": [
                        105,
                        128
                    ],
                    "url": "https://t.co/vPulAVwLCo"
                }
            ],
            "user_mentions": [
                {
                    "id": 14590758,
                    "id_str": "14590758",
                    "indices": [
                        3,
                        16
                    ],
                    "name": "Anna McMorrin MP",
                    "screen_name": "AnnaMcMorrin"
                },
                {
                    "id": 19701628,
                    "id_str": "19701628",
                    "indices": [
                        19,
                        23
                    ],
                    "name": "BBC",
                    "screen_name": "BBC"
                },
                {
                    "id": 23937508,
                    "id_str": "23937508",
                    "indices": [
                        24,
                        34
                    ],
                    "name": "BBC Radio 4",
                    "screen_name": "BBCRadio4"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 951124150634012678,
        "id_str": "951124150634012678",
        "in_reply_to_screen_name": null,
        "in_reply_to_status_id": null,
        "in_reply_to_status_id_str": null,
        "in_reply_to_user_id": null,
        "in_reply_to_user_id_str": null,
        "is_quote_status": true,
        "lang": "en",
        "metadata": {
            "iso_language_code": "en",
            "result_type": "recent"
        },
        "place": null,
        "possibly_sensitive": false,
        "quoted_status_id": 950764582284521474,
        "quoted_status_id_str": "950764582284521474",
        "retweet_count": 17,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Tue Jan 09 23:47:49 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/bbcnews/status\u2026",
                        "expanded_url": "https://twitter.com/bbcnews/status/950764582284521474",
                        "indices": [
                            87,
                            110
                        ],
                        "url": "https://t.co/vPulAVwLCo"
                    }
                ],
                "user_mentions": [
                    {
                        "id": 19701628,
                        "id_str": "19701628",
                        "indices": [
                            1,
                            5
                        ],
                        "name": "BBC",
                        "screen_name": "BBC"
                    },
                    {
                        "id": 23937508,
                        "id_str": "23937508",
                        "indices": [
                            6,
                            16
                        ],
                        "name": "BBC Radio 4",
                        "screen_name": "BBCRadio4"
                    }
                ]
            },
            "favorite_count": 24,
            "favorited": false,
            "geo": null,
            "id": 950876795053707265,
            "id_str": "950876795053707265",
            "in_reply_to_screen_name": null,
            "in_reply_to_status_id": null,
            "in_reply_to_status_id_str": null,
            "in_reply_to_user_id": null,
            "in_reply_to_user_id_str": null,
            "is_quote_status": true,
            "lang": "en",
            "metadata": {
                "iso_language_code": "en",
                "result_type": "recent"
            },
            "place": null,
            "possibly_sensitive": false,
            "quoted_status": {
                "contributors": null,
                "coordinates": null,
                "created_at": "Tue Jan 09 16:21:55 +0000 2018",
                "entities": {
                    "hashtags": [],
                    "symbols": [],
                    "urls": [
                        {
                            "display_url": "bbc.in/2Dcaupq",
                            "expanded_url": "http://bbc.in/2Dcaupq",
                            "indices": [
                                68,
                                91
                            ],
                            "url": "https://t.co/gvt4ZbO2QH"
                        }
                    ],
                    "user_mentions": []
                },
                "favorite_count": 132,
                "favorited": false,
                "geo": null,
                "id": 950764582284521474,
                "id_str": "950764582284521474",
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
                "retweet_count": 178,
                "retweeted": false,
                "source": "<a href=\"http://www.socialflow.com\" rel=\"nofollow\">SocialFlow</a>",
                "text": "Radio 4 host Winifred Robinson taken off air after gender pay tweet https://t.co/gvt4ZbO2QH",
                "truncated": false,
                "user": {
                    "contributors_enabled": false,
                    "created_at": "Mon Jan 08 08:05:57 +0000 2007",
                    "default_profile": false,
                    "default_profile_image": false,
                    "description": "News, features and analysis. For world news, follow @BBCWorld. Breaking news, follow @BBCBreaking. Latest sport news @BBCSport. Our Instagram: BBCNews",
                    "entities": {
                        "description": {
                            "urls": []
                        },
                        "url": {
                            "urls": [
                                {
                                    "display_url": "bbc.co.uk/news",
                                    "expanded_url": "http://www.bbc.co.uk/news",
                                    "indices": [
                                        0,
                                        23
                                    ],
                                    "url": "https://t.co/vBzl7LNCCQ"
                                }
                            ]
                        }
                    },
                    "favourites_count": 36,
                    "follow_request_sent": false,
                    "followers_count": 9109111,
                    "following": false,
                    "friends_count": 99,
                    "geo_enabled": true,
                    "has_extended_profile": false,
                    "id": 612473,
                    "id_str": "612473",
                    "is_translation_enabled": true,
                    "is_translator": false,
                    "lang": "en",
                    "listed_count": 39065,
                    "location": "London",
                    "name": "BBC News (UK)",
                    "notifications": false,
                    "profile_background_color": "FFFFFF",
                    "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/459296034556887040/n6vuQ9J2.jpeg",
                    "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/459296034556887040/n6vuQ9J2.jpeg",
                    "profile_background_tile": false,
                    "profile_banner_url": "https://pbs.twimg.com/profile_banners/612473/1467627928",
                    "profile_image_url": "http://pbs.twimg.com/profile_images/875702547016802304/9TC6qsAT_normal.jpg",
                    "profile_image_url_https": "https://pbs.twimg.com/profile_images/875702547016802304/9TC6qsAT_normal.jpg",
                    "profile_link_color": "1F527B",
                    "profile_sidebar_border_color": "FFFFFF",
                    "profile_sidebar_fill_color": "FFFFFF",
                    "profile_text_color": "5A5A5A",
                    "profile_use_background_image": true,
                    "protected": false,
                    "screen_name": "BBCNews",
                    "statuses_count": 343521,
                    "time_zone": "London",
                    "translator_type": "regular",
                    "url": "https://t.co/vBzl7LNCCQ",
                    "utc_offset": 0,
                    "verified": true
                }
            },
            "quoted_status_id": 950764582284521474,
            "quoted_status_id_str": "950764582284521474",
            "retweet_count": 17,
            "retweeted": false,
            "source": "<a href=\"http://twitter.com/download/iphone\" rel=\"nofollow\">Twitter for iPhone</a>",
            "text": ".@BBC @BBCRadio4 this is a very bad decision. Stop trying to shut down Women\u2019s voices. https://t.co/vPulAVwLCo",
            "truncated": false,
            "user": {
                "contributors_enabled": false,
                "created_at": "Tue Apr 29 20:56:18 +0000 2008",
                "default_profile": false,
                "default_profile_image": false,
                "description": "Welsh Labour MP for Cardiff North. Member of Environment Audit Select Committee. Shadow International Trade & Climate Change PPS. anna.mcmorrin.mp@parliament.uk",
                "entities": {
                    "description": {
                        "urls": []
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "annamcmorrin.co.uk",
                                "expanded_url": "http://www.annamcmorrin.co.uk",
                                "indices": [
                                    0,
                                    23
                                ],
                                "url": "https://t.co/NUIWderl24"
                            }
                        ]
                    }
                },
                "favourites_count": 1773,
                "follow_request_sent": false,
                "followers_count": 4701,
                "following": false,
                "friends_count": 4753,
                "geo_enabled": true,
                "has_extended_profile": true,
                "id": 14590758,
                "id_str": "14590758",
                "is_translation_enabled": false,
                "is_translator": false,
                "lang": "en",
                "listed_count": 120,
                "location": "Wales, United Kingdom",
                "name": "Anna McMorrin MP",
                "notifications": false,
                "profile_background_color": "000000",
                "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
                "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
                "profile_background_tile": false,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/14590758/1494111407",
                "profile_image_url": "http://pbs.twimg.com/profile_images/893548205794893828/huJiMI2Q_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/893548205794893828/huJiMI2Q_normal.jpg",
                "profile_link_color": "0084B4",
                "profile_sidebar_border_color": "000000",
                "profile_sidebar_fill_color": "000000",
                "profile_text_color": "000000",
                "profile_use_background_image": false,
                "protected": false,
                "screen_name": "AnnaMcMorrin",
                "statuses_count": 2059,
                "time_zone": "Pacific Time (US & Canada)",
                "translator_type": "none",
                "url": "https://t.co/NUIWderl24",
                "utc_offset": -28800,
                "verified": true
            }
        },
        "source": "<a href=\"https://mobile.twitter.com\" rel=\"nofollow\">Twitter Lite</a>",
        "text": "RT @AnnaMcMorrin: .@BBC @BBCRadio4 this is a very bad decision. Stop trying to shut down Women\u2019s voices. https://t.co/vPulAVwLCo",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Fri May 10 07:14:34 +0000 2013",
            "default_profile": true,
            "default_profile_image": false,
            "description": "Dedicate to the oppressed & arbitrarily detained",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 47675,
            "follow_request_sent": false,
            "followers_count": 3155,
            "following": false,
            "friends_count": 1664,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 1417427173,
            "id_str": "1417427173",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 80,
            "location": "Indonesia",
            "name": "#4Humanity",
            "notifications": false,
            "profile_background_color": "C0DEED",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
            "profile_background_tile": false,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/1417427173/1513606473",
            "profile_image_url": "http://pbs.twimg.com/profile_images/940174030128087041/W3E3Vrfj_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/940174030128087041/W3E3Vrfj_normal.jpg",
            "profile_link_color": "1DA1F2",
            "profile_sidebar_border_color": "C0DEED",
            "profile_sidebar_fill_color": "DDEEF6",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "Me_AYS",
            "statuses_count": 101068,
            "time_zone": null,
            "translator_type": "none",
            "url": null,
            "utc_offset": null,
            "verified": false
        }
    }
    
    
    Source @BBC: 
    
    
    Tweet 1: RT @AnnaMcMorrin: .@BBC @BBCRadio4 this is a very bad decision. Stop trying to shut down Women’s voices. https://t.co/vPulAVwLCo
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': 0.013868000000000009, 'Positive': 0.065240000000000006, 'Neutral': 0.060299999999999992, 'Negative': 0.87447000000000008, 'Tweet_Count': 100}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Wed Jan 10 16:10:57 +0000 2018",
        "entities": {
            "hashtags": [],
            "media": [
                {
                    "display_url": "pic.twitter.com/igf0mQgm1d",
                    "expanded_url": "https://twitter.com/ChanBahlumII/status/951124212219031552/photo/1",
                    "id": 951124207500324864,
                    "id_str": "951124207500324864",
                    "indices": [
                        6,
                        29
                    ],
                    "media_url": "http://pbs.twimg.com/media/DTMSbW1U8AAb2R1.jpg",
                    "media_url_https": "https://pbs.twimg.com/media/DTMSbW1U8AAb2R1.jpg",
                    "sizes": {
                        "large": {
                            "h": 439,
                            "resize": "fit",
                            "w": 306
                        },
                        "medium": {
                            "h": 439,
                            "resize": "fit",
                            "w": 306
                        },
                        "small": {
                            "h": 439,
                            "resize": "fit",
                            "w": 306
                        },
                        "thumb": {
                            "h": 150,
                            "resize": "crop",
                            "w": 150
                        }
                    },
                    "type": "photo",
                    "url": "https://t.co/igf0mQgm1d"
                }
            ],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 759251,
                    "id_str": "759251",
                    "indices": [
                        0,
                        4
                    ],
                    "name": "CNN",
                    "screen_name": "CNN"
                }
            ]
        },
        "extended_entities": {
            "media": [
                {
                    "display_url": "pic.twitter.com/igf0mQgm1d",
                    "expanded_url": "https://twitter.com/ChanBahlumII/status/951124212219031552/photo/1",
                    "id": 951124207500324864,
                    "id_str": "951124207500324864",
                    "indices": [
                        6,
                        29
                    ],
                    "media_url": "http://pbs.twimg.com/media/DTMSbW1U8AAb2R1.jpg",
                    "media_url_https": "https://pbs.twimg.com/media/DTMSbW1U8AAb2R1.jpg",
                    "sizes": {
                        "large": {
                            "h": 439,
                            "resize": "fit",
                            "w": 306
                        },
                        "medium": {
                            "h": 439,
                            "resize": "fit",
                            "w": 306
                        },
                        "small": {
                            "h": 439,
                            "resize": "fit",
                            "w": 306
                        },
                        "thumb": {
                            "h": 150,
                            "resize": "crop",
                            "w": 150
                        }
                    },
                    "type": "photo",
                    "url": "https://t.co/igf0mQgm1d"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 951124212219031552,
        "id_str": "951124212219031552",
        "in_reply_to_screen_name": "CNN",
        "in_reply_to_status_id": 951123994547294208,
        "in_reply_to_status_id_str": "951123994547294208",
        "in_reply_to_user_id": 759251,
        "in_reply_to_user_id_str": "759251",
        "is_quote_status": false,
        "lang": "und",
        "metadata": {
            "iso_language_code": "und",
            "result_type": "recent"
        },
        "place": null,
        "possibly_sensitive": false,
        "retweet_count": 0,
        "retweeted": false,
        "source": "<a href=\"http://twitter.com/download/android\" rel=\"nofollow\">Twitter for Android</a>",
        "text": "@CNN  https://t.co/igf0mQgm1d",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Fri Nov 13 22:43:37 +0000 2009",
            "default_profile": false,
            "default_profile_image": false,
            "description": "",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 9409,
            "follow_request_sent": false,
            "followers_count": 4031,
            "following": false,
            "friends_count": 4014,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 89812517,
            "id_str": "89812517",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 43,
            "location": "",
            "name": "ChanBahlum II",
            "notifications": false,
            "profile_background_color": "000000",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme3/bg.gif",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme3/bg.gif",
            "profile_background_tile": false,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/89812517/1510872682",
            "profile_image_url": "http://pbs.twimg.com/profile_images/932505533252362240/HhozJlCY_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/932505533252362240/HhozJlCY_normal.jpg",
            "profile_link_color": "FFFFFF",
            "profile_sidebar_border_color": "000000",
            "profile_sidebar_fill_color": "000000",
            "profile_text_color": "000000",
            "profile_use_background_image": false,
            "protected": false,
            "screen_name": "ChanBahlumII",
            "statuses_count": 13086,
            "time_zone": "Mountain Time (US & Canada)",
            "translator_type": "none",
            "url": null,
            "utc_offset": -25200,
            "verified": false
        }
    }
    
    
    Source @CNN: 
    
    
    Tweet 1: @CNN  https://t.co/igf0mQgm1d
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': 0.013868000000000009, 'Positive': 0.065240000000000006, 'Neutral': 0.060299999999999992, 'Negative': 0.87447000000000008, 'Tweet_Count': 100}, {'User': '@CNN', 'color': 'red', 'Compound': -0.12869100000000003, 'Positive': 0.05414999999999999, 'Neutral': 0.089849999999999999, 'Negative': 0.85596000000000005, 'Tweet_Count': 100}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Wed Jan 10 16:10:52 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 15012486,
                    "id_str": "15012486",
                    "indices": [
                        3,
                        11
                    ],
                    "name": "CBS News",
                    "screen_name": "CBSNews"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 951124191343869952,
        "id_str": "951124191343869952",
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
        "retweet_count": 9135,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Tue Jan 09 18:11:47 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/i/web/status/9\u2026",
                        "expanded_url": "https://twitter.com/i/web/status/950792229399998465",
                        "indices": [
                            117,
                            140
                        ],
                        "url": "https://t.co/iV0sNuXig9"
                    }
                ],
                "user_mentions": []
            },
            "favorite_count": 7126,
            "favorited": false,
            "geo": null,
            "id": 950792229399998465,
            "id_str": "950792229399998465",
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
            "retweet_count": 9135,
            "retweeted": false,
            "source": "<a href=\"https://www.sprinklr.com\" rel=\"nofollow\">Sprinklr</a>",
            "text": "\"Are you kidding me... you just pushed me to the floor.\" A teacher was forcibly removed and handcuffed after speaki\u2026 https://t.co/iV0sNuXig9",
            "truncated": true,
            "user": {
                "contributors_enabled": false,
                "created_at": "Thu Jun 05 00:54:31 +0000 2008",
                "default_profile": false,
                "default_profile_image": false,
                "description": "Your source for original reporting and trusted news.",
                "entities": {
                    "description": {
                        "urls": []
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "CBSNews.com",
                                "expanded_url": "http://CBSNews.com",
                                "indices": [
                                    0,
                                    23
                                ],
                                "url": "https://t.co/VGut7r2Vg5"
                            }
                        ]
                    }
                },
                "favourites_count": 270,
                "follow_request_sent": false,
                "followers_count": 6336643,
                "following": false,
                "friends_count": 429,
                "geo_enabled": false,
                "has_extended_profile": false,
                "id": 15012486,
                "id_str": "15012486",
                "is_translation_enabled": true,
                "is_translator": false,
                "lang": "en",
                "listed_count": 47015,
                "location": "New York, NY",
                "name": "CBS News",
                "notifications": false,
                "profile_background_color": "D9DADA",
                "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/736106551/37bf1f784305fe4a9c7e9105772c6e1a.jpeg",
                "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/736106551/37bf1f784305fe4a9c7e9105772c6e1a.jpeg",
                "profile_background_tile": false,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/15012486/1514512362",
                "profile_image_url": "http://pbs.twimg.com/profile_images/645966750941626368/d0Q4voGK_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/645966750941626368/d0Q4voGK_normal.jpg",
                "profile_link_color": "B12124",
                "profile_sidebar_border_color": "FFFFFF",
                "profile_sidebar_fill_color": "EAEDF0",
                "profile_text_color": "000000",
                "profile_use_background_image": true,
                "protected": false,
                "screen_name": "CBSNews",
                "statuses_count": 157780,
                "time_zone": "Eastern Time (US & Canada)",
                "translator_type": "none",
                "url": "https://t.co/VGut7r2Vg5",
                "utc_offset": -18000,
                "verified": true
            }
        },
        "source": "<a href=\"http://twitter.com/download/android\" rel=\"nofollow\">Twitter for Android</a>",
        "text": "RT @CBSNews: \"Are you kidding me... you just pushed me to the floor.\" A teacher was forcibly removed and handcuffed after speaking up about\u2026",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Thu May 12 16:46:24 +0000 2011",
            "default_profile": false,
            "default_profile_image": false,
            "description": "@wemadeitpodcast co-host. Free Discussion Society creator. \nDo what you love and the money will follow",
            "entities": {
                "description": {
                    "urls": []
                },
                "url": {
                    "urls": [
                        {
                            "display_url": "wemadeit.syndicate23.co",
                            "expanded_url": "http://wemadeit.syndicate23.co",
                            "indices": [
                                0,
                                23
                            ],
                            "url": "https://t.co/Zwjw2Axaec"
                        }
                    ]
                }
            },
            "favourites_count": 266,
            "follow_request_sent": false,
            "followers_count": 173,
            "following": false,
            "friends_count": 127,
            "geo_enabled": false,
            "has_extended_profile": false,
            "id": 297511498,
            "id_str": "297511498",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 10,
            "location": "socal",
            "name": "Bam",
            "notifications": false,
            "profile_background_color": "C0DEED",
            "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/278295839/tulip_field.jpg",
            "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/278295839/tulip_field.jpg",
            "profile_background_tile": true,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/297511498/1493968843",
            "profile_image_url": "http://pbs.twimg.com/profile_images/860393105165492224/C8KCvPnU_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/860393105165492224/C8KCvPnU_normal.jpg",
            "profile_link_color": "0084B4",
            "profile_sidebar_border_color": "C0DEED",
            "profile_sidebar_fill_color": "DDEEF6",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "marchleonard",
            "statuses_count": 33325,
            "time_zone": "Pacific Time (US & Canada)",
            "translator_type": "none",
            "url": "https://t.co/Zwjw2Axaec",
            "utc_offset": -28800,
            "verified": false
        }
    }
    
    
    Source @CBSNews: 
    
    
    Tweet 1: RT @CBSNews: "Are you kidding me... you just pushed me to the floor." A teacher was forcibly removed and handcuffed after speaking up about…
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': 0.013868000000000009, 'Positive': 0.065240000000000006, 'Neutral': 0.060299999999999992, 'Negative': 0.87447000000000008, 'Tweet_Count': 100}, {'User': '@CNN', 'color': 'red', 'Compound': -0.12869100000000003, 'Positive': 0.05414999999999999, 'Neutral': 0.089849999999999999, 'Negative': 0.85596000000000005, 'Tweet_Count': 100}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.054511999999999998, 'Positive': 0.073690000000000005, 'Neutral': 0.054870000000000002, 'Negative': 0.87143999999999988, 'Tweet_Count': 100}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Wed Jan 10 16:10:59 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [
                {
                    "display_url": "twitter.com/i/web/status/9\u2026",
                    "expanded_url": "https://twitter.com/i/web/status/951124220511219712",
                    "indices": [
                        120,
                        143
                    ],
                    "url": "https://t.co/xHUbZxeSSn"
                }
            ],
            "user_mentions": [
                {
                    "id": 1367531,
                    "id_str": "1367531",
                    "indices": [
                        0,
                        8
                    ],
                    "name": "Fox News",
                    "screen_name": "FoxNews"
                },
                {
                    "id": 44951059,
                    "id_str": "44951059",
                    "indices": [
                        9,
                        24
                    ],
                    "name": "Sheriff Joe Arpaio",
                    "screen_name": "RealSheriffJoe"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 951124220511219712,
        "id_str": "951124220511219712",
        "in_reply_to_screen_name": "FoxNews",
        "in_reply_to_status_id": 951115646724603907,
        "in_reply_to_status_id_str": "951115646724603907",
        "in_reply_to_user_id": 1367531,
        "in_reply_to_user_id_str": "1367531",
        "is_quote_status": false,
        "lang": "en",
        "metadata": {
            "iso_language_code": "en",
            "result_type": "recent"
        },
        "place": null,
        "retweet_count": 0,
        "retweeted": false,
        "source": "<a href=\"http://twitter.com/#!/download/ipad\" rel=\"nofollow\">Twitter for iPad</a>",
        "text": "@FoxNews @RealSheriffJoe FauxGnus \u2014 your hire a Nazi to talk on mike &amp; now you give this convicted bigot airtime \u2014\u2026 https://t.co/xHUbZxeSSn",
        "truncated": true,
        "user": {
            "contributors_enabled": false,
            "created_at": "Fri Mar 27 15:22:49 +0000 2009",
            "default_profile": false,
            "default_profile_image": false,
            "description": "Rolling strikes when I can.",
            "entities": {
                "description": {
                    "urls": []
                },
                "url": {
                    "urls": [
                        {
                            "display_url": "dudeism.com",
                            "expanded_url": "http://dudeism.com/",
                            "indices": [
                                0,
                                23
                            ],
                            "url": "https://t.co/B0xWgmH6xp"
                        }
                    ]
                }
            },
            "favourites_count": 2674,
            "follow_request_sent": false,
            "followers_count": 746,
            "following": false,
            "friends_count": 889,
            "geo_enabled": true,
            "has_extended_profile": false,
            "id": 27025237,
            "id_str": "27025237",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 36,
            "location": "The Dude will abide.",
            "name": "Artfull Duderino",
            "notifications": false,
            "profile_background_color": "642D8B",
            "profile_background_image_url": "http://abs.twimg.com/images/themes/theme10/bg.gif",
            "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme10/bg.gif",
            "profile_background_tile": true,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/27025237/1353075367",
            "profile_image_url": "http://pbs.twimg.com/profile_images/880164745592098816/n9sPxWhX_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/880164745592098816/n9sPxWhX_normal.jpg",
            "profile_link_color": "FF0000",
            "profile_sidebar_border_color": "65B0DA",
            "profile_sidebar_fill_color": "7AC3EE",
            "profile_text_color": "3D1957",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "dodgr007",
            "statuses_count": 61157,
            "time_zone": "Eastern Time (US & Canada)",
            "translator_type": "none",
            "url": "https://t.co/B0xWgmH6xp",
            "utc_offset": -18000,
            "verified": false
        }
    }
    
    
    Source @FoxNews: 
    
    
    Tweet 1: @FoxNews @RealSheriffJoe FauxGnus — your hire a Nazi to talk on mike &amp; now you give this convicted bigot airtime —… https://t.co/xHUbZxeSSn
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': 0.013868000000000009, 'Positive': 0.065240000000000006, 'Neutral': 0.060299999999999992, 'Negative': 0.87447000000000008, 'Tweet_Count': 100}, {'User': '@CNN', 'color': 'red', 'Compound': -0.12869100000000003, 'Positive': 0.05414999999999999, 'Neutral': 0.089849999999999999, 'Negative': 0.85596000000000005, 'Tweet_Count': 100}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.054511999999999998, 'Positive': 0.073690000000000005, 'Neutral': 0.054870000000000002, 'Negative': 0.87143999999999988, 'Tweet_Count': 100}, {'User': '@FoxNews', 'color': 'blue', 'Compound': -0.00044099999999998698, 'Positive': 0.084859999999999991, 'Neutral': 0.071340000000000001, 'Negative': 0.84377000000000013, 'Tweet_Count': 100}]
    
    {
        "contributors": null,
        "coordinates": null,
        "created_at": "Wed Jan 10 16:11:01 +0000 2018",
        "entities": {
            "hashtags": [],
            "symbols": [],
            "urls": [],
            "user_mentions": [
                {
                    "id": 120600609,
                    "id_str": "120600609",
                    "indices": [
                        3,
                        16
                    ],
                    "name": "Sterling J Sterling",
                    "screen_name": "Sterlingartz"
                },
                {
                    "id": 736959136164806657,
                    "id_str": "736959136164806657",
                    "indices": [
                        18,
                        29
                    ],
                    "name": "Jacob Wohl",
                    "screen_name": "JacobAWohl"
                },
                {
                    "id": 25073877,
                    "id_str": "25073877",
                    "indices": [
                        30,
                        46
                    ],
                    "name": "Donald J. Trump",
                    "screen_name": "realDonaldTrump"
                },
                {
                    "id": 807095,
                    "id_str": "807095",
                    "indices": [
                        47,
                        55
                    ],
                    "name": "The New York Times",
                    "screen_name": "nytimes"
                }
            ]
        },
        "favorite_count": 0,
        "favorited": false,
        "geo": null,
        "id": 951124228014858241,
        "id_str": "951124228014858241",
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
        "retweet_count": 21,
        "retweeted": false,
        "retweeted_status": {
            "contributors": null,
            "coordinates": null,
            "created_at": "Wed Jan 10 15:16:20 +0000 2018",
            "entities": {
                "hashtags": [],
                "symbols": [],
                "urls": [
                    {
                        "display_url": "twitter.com/i/web/status/9\u2026",
                        "expanded_url": "https://twitter.com/i/web/status/951110464502673408",
                        "indices": [
                            117,
                            140
                        ],
                        "url": "https://t.co/EJpdOzBEyp"
                    }
                ],
                "user_mentions": [
                    {
                        "id": 736959136164806657,
                        "id_str": "736959136164806657",
                        "indices": [
                            0,
                            11
                        ],
                        "name": "Jacob Wohl",
                        "screen_name": "JacobAWohl"
                    },
                    {
                        "id": 25073877,
                        "id_str": "25073877",
                        "indices": [
                            12,
                            28
                        ],
                        "name": "Donald J. Trump",
                        "screen_name": "realDonaldTrump"
                    },
                    {
                        "id": 807095,
                        "id_str": "807095",
                        "indices": [
                            29,
                            37
                        ],
                        "name": "The New York Times",
                        "screen_name": "nytimes"
                    }
                ]
            },
            "favorite_count": 181,
            "favorited": false,
            "geo": null,
            "id": 951110464502673408,
            "id_str": "951110464502673408",
            "in_reply_to_screen_name": "Sterlingartz",
            "in_reply_to_status_id": 951109712229097473,
            "in_reply_to_status_id_str": "951109712229097473",
            "in_reply_to_user_id": 120600609,
            "in_reply_to_user_id_str": "120600609",
            "is_quote_status": false,
            "lang": "en",
            "metadata": {
                "iso_language_code": "en",
                "result_type": "recent"
            },
            "place": null,
            "possibly_sensitive": false,
            "retweet_count": 21,
            "retweeted": false,
            "source": "<a href=\"http://twitter.com\" rel=\"nofollow\">Twitter Web Client</a>",
            "text": "@JacobAWohl @realDonaldTrump @nytimes 14 counts of securities fraud\nDisgraced Teen Hedge Fund Manager Jacob Wohl Ha\u2026 https://t.co/EJpdOzBEyp",
            "truncated": true,
            "user": {
                "contributors_enabled": false,
                "created_at": "Sat Mar 06 23:48:40 +0000 2010",
                "default_profile": false,
                "default_profile_image": false,
                "description": "well traveled indie artist/writer living the good life in an old barn/studio on a bucolic rural mountain. #Resist \ud83c\udff3\ufe0f\u200d\ud83c\udf08https://t.co/K78tvhNhQG",
                "entities": {
                    "description": {
                        "urls": [
                            {
                                "display_url": "facebook.com/Sterlingarts/",
                                "expanded_url": "https://www.facebook.com/Sterlingarts/",
                                "indices": [
                                    118,
                                    141
                                ],
                                "url": "https://t.co/K78tvhNhQG"
                            }
                        ]
                    },
                    "url": {
                        "urls": [
                            {
                                "display_url": "flickr.com/photos/sterlin\u2026",
                                "expanded_url": "https://www.flickr.com/photos/sterlingartz/",
                                "indices": [
                                    0,
                                    23
                                ],
                                "url": "https://t.co/c72NAAs7Kj"
                            }
                        ]
                    }
                },
                "favourites_count": 19146,
                "follow_request_sent": false,
                "followers_count": 2496,
                "following": false,
                "friends_count": 3245,
                "geo_enabled": false,
                "has_extended_profile": true,
                "id": 120600609,
                "id_str": "120600609",
                "is_translation_enabled": false,
                "is_translator": false,
                "lang": "en",
                "listed_count": 26,
                "location": "hudson valley",
                "name": "Sterling J Sterling",
                "notifications": false,
                "profile_background_color": "131516",
                "profile_background_image_url": "http://abs.twimg.com/images/themes/theme14/bg.gif",
                "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme14/bg.gif",
                "profile_background_tile": true,
                "profile_banner_url": "https://pbs.twimg.com/profile_banners/120600609/1487478734",
                "profile_image_url": "http://pbs.twimg.com/profile_images/846849770945593344/f3GfzfKU_normal.jpg",
                "profile_image_url_https": "https://pbs.twimg.com/profile_images/846849770945593344/f3GfzfKU_normal.jpg",
                "profile_link_color": "1B95E0",
                "profile_sidebar_border_color": "FFFFFF",
                "profile_sidebar_fill_color": "EFEFEF",
                "profile_text_color": "333333",
                "profile_use_background_image": true,
                "protected": false,
                "screen_name": "Sterlingartz",
                "statuses_count": 27772,
                "time_zone": "Eastern Time (US & Canada)",
                "translator_type": "none",
                "url": "https://t.co/c72NAAs7Kj",
                "utc_offset": -18000,
                "verified": false
            }
        },
        "source": "<a href=\"http://twitter.com/download/iphone\" rel=\"nofollow\">Twitter for iPhone</a>",
        "text": "RT @Sterlingartz: @JacobAWohl @realDonaldTrump @nytimes 14 counts of securities fraud\nDisgraced Teen Hedge Fund Manager Jacob Wohl Has Star\u2026",
        "truncated": false,
        "user": {
            "contributors_enabled": false,
            "created_at": "Mon Apr 27 23:24:32 +0000 2009",
            "default_profile": false,
            "default_profile_image": false,
            "description": "",
            "entities": {
                "description": {
                    "urls": []
                }
            },
            "favourites_count": 11010,
            "follow_request_sent": false,
            "followers_count": 115,
            "following": false,
            "friends_count": 409,
            "geo_enabled": true,
            "has_extended_profile": false,
            "id": 35902826,
            "id_str": "35902826",
            "is_translation_enabled": false,
            "is_translator": false,
            "lang": "en",
            "listed_count": 1,
            "location": "Virginia Beach, VA",
            "name": "Essence Watford",
            "notifications": false,
            "profile_background_color": "DBE9ED",
            "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/409898988/Essence_Howard.JPG",
            "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/409898988/Essence_Howard.JPG",
            "profile_background_tile": true,
            "profile_banner_url": "https://pbs.twimg.com/profile_banners/35902826/1479782157",
            "profile_image_url": "http://pbs.twimg.com/profile_images/901247077027545092/5fG05nGe_normal.jpg",
            "profile_image_url_https": "https://pbs.twimg.com/profile_images/901247077027545092/5fG05nGe_normal.jpg",
            "profile_link_color": "CC3366",
            "profile_sidebar_border_color": "DBE9ED",
            "profile_sidebar_fill_color": "E6F6F9",
            "profile_text_color": "333333",
            "profile_use_background_image": true,
            "protected": false,
            "screen_name": "EssenceLove",
            "statuses_count": 4470,
            "time_zone": "Eastern Time (US & Canada)",
            "translator_type": "none",
            "url": null,
            "utc_offset": -18000,
            "verified": false
        }
    }
    
    
    Source @nytimes: 
    
    
    Tweet 1: RT @Sterlingartz: @JacobAWohl @realDonaldTrump @nytimes 14 counts of securities fraud
    Disgraced Teen Hedge Fund Manager Jacob Wohl Has Star…
    [{'User': '@BBC', 'color': 'skyblue', 'Compound': 0.013868000000000009, 'Positive': 0.065240000000000006, 'Neutral': 0.060299999999999992, 'Negative': 0.87447000000000008, 'Tweet_Count': 100}, {'User': '@CNN', 'color': 'red', 'Compound': -0.12869100000000003, 'Positive': 0.05414999999999999, 'Neutral': 0.089849999999999999, 'Negative': 0.85596000000000005, 'Tweet_Count': 100}, {'User': '@CBSNews', 'color': 'green', 'Compound': 0.054511999999999998, 'Positive': 0.073690000000000005, 'Neutral': 0.054870000000000002, 'Negative': 0.87143999999999988, 'Tweet_Count': 100}, {'User': '@FoxNews', 'color': 'blue', 'Compound': -0.00044099999999998698, 'Positive': 0.084859999999999991, 'Neutral': 0.071340000000000001, 'Negative': 0.84377000000000013, 'Tweet_Count': 100}, {'User': '@nytimes', 'color': 'yellow', 'Compound': 0.046898999999999996, 'Positive': 0.076319999999999999, 'Neutral': 0.060659999999999999, 'Negative': 0.86302000000000012, 'Tweet_Count': 100}]
    



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
      <td>-0.7397</td>
      <td>Wed Jan 10 16:10:43 +0000 2018</td>
      <td>0.294</td>
      <td>0.706</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>1</td>
      <td>RT @AnnaMcMorrin: .@BBC @BBCRadio4 this is a v...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.2960</td>
      <td>Wed Jan 10 16:10:42 +0000 2018</td>
      <td>0.099</td>
      <td>0.901</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>2</td>
      <td>The @BBC program #HouseofSaud is not balanced....</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0000</td>
      <td>Wed Jan 10 16:10:36 +0000 2018</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>3</td>
      <td>RT @BBC: 🚀✨ David Bowie changed music forever....</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0000</td>
      <td>Wed Jan 10 16:10:17 +0000 2018</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>4</td>
      <td>#CES 2018: @Kodak's plans for the #KodakCoin a...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.7750</td>
      <td>Wed Jan 10 16:10:09 +0000 2018</td>
      <td>0.040</td>
      <td>0.730</td>
      <td>0.231</td>
      <td>@BBC</td>
      <td>5</td>
      <td>RT @TomLondon6: Of course @BBC must deal urgen...</td>
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
      <td>0.013868</td>
      <td>0.87447</td>
      <td>0.06030</td>
      <td>0.06524</td>
      <td>100</td>
      <td>@BBC</td>
      <td>skyblue</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.128691</td>
      <td>0.85596</td>
      <td>0.08985</td>
      <td>0.05415</td>
      <td>100</td>
      <td>@CNN</td>
      <td>red</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.054512</td>
      <td>0.87144</td>
      <td>0.05487</td>
      <td>0.07369</td>
      <td>100</td>
      <td>@CBSNews</td>
      <td>green</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.000441</td>
      <td>0.84377</td>
      <td>0.07134</td>
      <td>0.08486</td>
      <td>100</td>
      <td>@FoxNews</td>
      <td>blue</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.046899</td>
      <td>0.86302</td>
      <td>0.06066</td>
      <td>0.07632</td>
      <td>100</td>
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
plt.title("Sentiment Analysis of Media Tweets (01/10/18)")
plt.ylabel("Tweet Polarity")
plt.xlabel("Tweets Ago")
#plt.xlim((205, -5)) # x-axis is for 200 tweets
plt.xlim((105, -5)) # x-axis is for 100 tweets
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
    0 -0.013201   0.88025  0.06361   0.05615          100  @BBC  skyblue



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
ax.set_title('Overall Media Sentiment based on Twitter (01/10/18)')
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
    -0.1
    -0.02
    0.01


    /Users/sunitharamakrishnan/anaconda3/envs/PythonData/lib/python3.6/site-packages/matplotlib/figure.py:418: UserWarning: matplotlib is currently using a non-GUI backend, so cannot show the figure
      "matplotlib is currently using a non-GUI backend, "



```python
#Overall_media_sentiment_based_on_twitter.png
```
