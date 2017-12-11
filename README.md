# HW-Twitter

import json
import time 
import tweepy
import matplotlib.pyplot as plt
import apikeys
import pandas as pd
import datetime as dt
import matplotlib.pyplot as plt

# Twitter API Keys
consumer_key = apikeys.TWITTER_CONSUMER_KEY
consumer_secret = apikeys.TWITTER_CONSUMER_SECRET
access_token = apikeys.TWITTER_ACCESS_TOKEN
access_token_secret = apikeys.TWITTER_ACCESS_TOKEN_SECRET
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()

# Setup Tweepy API Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())
timestamp=[]
text=[]
screen_name=[]
positive=[]
negative=[]
neutral=[]
symbol=[]
compound=[]
week=[]
count=0
  


#instead of 100 tweets i want to expand this project to last 50 pages of tweets for each user

target_users=["@CNN","@CBS", "@BBC","@FOX","@nytimes"]

for user in target_users:
   
        
    for page in range (50):

        tweets=api.user_timeline(user, count=900, page=page)
        for tweet in tweets:
            
            
            
            timestamp.append(dt.datetime.strptime(tweet["created_at"], "%a %b %d %X %z %Y").strftime("%b/%d/%Y"))
            week.append(dt.datetime.strptime(tweet["created_at"], "%a %b %d %X %z %Y").strftime("%V"))
            #timestamp.append(tweet["created_at"])
            #text.append(tweet["text"]) # to much data we dont read actual tweet
                
            screen_name.append(tweet["user"]["screen_name"])
                
            scores = analyzer.polarity_scores(tweet["text"])
            comp = scores['compound']
            pos = scores['pos']
            neut = scores['neu']
            neg = scores['neg']

            compound.append(comp)
            positive.append(pos)
            negative.append(neg)
            neutral.append(neut)
        count+=1
        # add time sleep for two minutes for API call limit
        if count%25 ==0:
            time.sleep(120)

    #time.sleep(60)
    
    
tweeterDF = pd.DataFrame({'name':screen_name,'timestamp':timestamp,'week':week, 
                            'compound':compound , 'positive':positive, 'negative':negative , 'neutral':neutral})
tweeterDF = tweeterDF[['name', 'timestamp','week','compound', 'positive', 'negative', 'neutral']]
tweeterDF 

import seaborn as sns
import numpy as np
ind = np.arange(len(target_users))
sns.set_style('whitegrid')
%matplotlib inline


plt.figure(figsize = (10,10))

colors=('Black',"Blue", "red","Gold","Green")


plot=plt.bar (ind, tweeterDF.groupby("name").compound.mean(),align='center', 
              color=colors,label=target_users, tick_label=target_users )

plt.legend()
plt.savefig('barchart.png')

plt.figure(figsize = (30,30))

x=np.arange(len(tweeterDF[tweeterDF["name"]=="BBC"]))



# plt.scatter(x,tweeterDF[tweeterDF["name"]=="BBC"]["compound"], marker="o", facecolors="red",
#            edgecolors="black", label="BBC", alpha=0.75)


plt.plot(x,tweeterDF[tweeterDF["name"]=="BBC"]["compound"],color="Blue",
            label="BBC")
plt.title("Sentiment Analysis of Tweets")
plt.ylabel("sentiment")
plt.xlabel("tweetPolarity")
plt.legend()
plt.savefig('BBC-plot.png')

plt.figure(figsize = (15,15))

x=np.arange(len(tweeterDF[tweeterDF["name"]=="BBC"]))



plt.scatter(x,tweeterDF[tweeterDF["name"]=="CNN"]["compound"], marker="o", facecolors="Black",
           edgecolors="black", label="BBC", alpha=0.75)



plt.title("Sentiment Analysis of Tweets")
plt.ylabel("sentiment")
plt.xlabel("tweetPolarity")
plt.legend()
plt.savefig('BBC-Scatter.png')


plt.figure(figsize = (20,20))

x=np.arange(len(tweeterDF[tweeterDF["name"]=="CNN"]))



# plt.scatter(x,tweeterDF[tweeterDF["name"]=="BBC"]["compound"], marker="o", facecolors="red",
#            edgecolors="black", label="BBC", alpha=0.75)


plt.plot(x,tweeterDF[tweeterDF["name"]=="CNN"]["compound"],color="Blue",
            label="CNN")
plt.title("Sentiment Analysis of Tweets")
plt.ylabel("sentiment")
plt.xlabel("tweetPolarity")
plt.legend()
plt.savefig('CNN-plot.png')


plt.figure(figsize = (15,15))

x=np.arange(len(tweeterDF[tweeterDF["name"]=="CNN"]))



plt.scatter(x,tweeterDF[tweeterDF["name"]=="CNN"]["compound"], marker="o", facecolors="red",
           edgecolors="black", label="CNN", alpha=0.75)


#plt.plot(x,tweeterDF[tweeterDF["name"]=="BBC"]["compound"],color="Blue",
            #label="BBC")
plt.title("Sentiment Analysis of Tweets")
plt.ylabel("sentiment")
plt.xlabel("tweetPolarity")
plt.legend()
plt.savefig('CNN-Scatter.png')


plt.figure(figsize = (15,15))

x=np.arange(len(tweeterDF[tweeterDF["name"]=="nytimes"]))



# plt.scatter(x,tweeterDF[tweeterDF["name"]=="BBC"]["compound"], marker="o", facecolors="red",
#            edgecolors="black", label="BBC", alpha=0.75)


plt.plot(x,tweeterDF[tweeterDF["name"]=="nytimes"]["compound"],color="Blue",
            label="nytimes")
plt.title("Sentiment Analysis of Tweets")
plt.ylabel("sentiment")
plt.xlabel("tweetPolarity")
plt.legend()
plt.savefig('NYT-plot.png')

plt.figure(figsize = (15,15))

x=np.arange(len(tweeterDF[tweeterDF["name"]=="CBS"]))





plt.plot(x,tweeterDF[tweeterDF["name"]=="CBS"]["compound"],color="Green",
            label="CBS")
plt.title("Sentiment Analysis of Tweets")
plt.ylabel("sentiment")
plt.xlabel("tweetPolarity")
plt.legend()
plt.savefig('CBS-plot.png')
