import string
import time
import sys
import tweepy
from datetime import date, timedelta
#enter the corresponding information from your Twitter application:
CONSUMER_KEY = 'jULreBktd9V8XOZ3JeXAkg'
CONSUMER_SECRET = 'xSj5xCwdbORRjO9hMX7yq4yYCOAMyHRxRePuigVhI'
ACCESS_KEY = '156611619-TioAOAAbD5bF6JErJI2vJZl0f5kc0kFSNhiaGL15'
ACCESS_SECRET = 'XaA12nzvflw5Xzq6BoiMyU9jh6FWCl6LDsjGIx32QxOl6'
auth = tweepy.OAuthHandler(CONSUMER_KEY, CONSUMER_SECRET)
auth.set_access_token(ACCESS_KEY, ACCESS_SECRET)
api = tweepy.API(auth)
geo_file = open('us_break_up.csv')
movie_file = open('movies')
geo_dir = {}
movies = []
for line in movie_file:
    movies.append(line.rstrip())
count = 1
for line in geo_file:
    line = line.rstrip()
    lat = line.split(',')[0]
    long1 = line.split(',')[1]
    value = lat + ',' + long1 + ',144mi'
    geo_dir[str(count)] = value
    count += 1
days_of_week = []
today = date.today()
for i in range(1):
    day = today - timedelta(days=i)
    days_of_week.append(str(day))

for movie in movies:
    tweet_file = open('%s' % movie, 'w')
    print "Getting Twitter feeds for -->", movie
    for key in geo_dir:
        geo_loc = geo_dir[key]
        hash_tag_movie = '#' + movie
        for day in days_of_week:
	    time.sleep(2)
	    search = api.search(q=hash_tag_movie, geocode=geo_loc, until=day, count=100)
	    print "Still Feeding....This is a glutunous program"
	    for tweet in search:
		try:
		    tweet_file.write((tweet.text.replace('\n',' ')) + '~~~' + geo_loc.split(',')[0]+','+geo_loc.split(',')[1] + '~~~' + str(tweet.retweet_count) + '~~~' +(tweet.user.screen_name) + '~~~' + str(tweet.created_at.date()) + '\n')
		    tweet_file.flush()
		except:
		    pass    
    tweet_file.close()
