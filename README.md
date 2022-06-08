# Database-Design

## All about database design- why do we need it?
Database design is the most important process in terms of Software engineering because it's the strong backbone to make efficient programs that run smoothly over the front end which needs effective querying!

## Don't you think a single table handles the requirements well?

Yeah, having a single table for the entire database is simple but this makes things complex to handle various operations related to the data and in other terms, it just makes things messy and won't be an effective solution considering the future!

That's the reason why Database normalization principles are available for us so that a simple and effective split up of tables can be possible with the help of various functional dependencies so that we have a better way of handling things which is easily maintainable and future efficient which is accessible and more open for updates or any kind of operations without much difficulties!



This is a database design in third normal form for Twitter application scenario where the user posts the tweets which contains text and hashtags.

This is where more data needed!

## USER_TIME_ZONE_OFFSET: 

- user_time_zone character varying (127) primary key,
- user_utc_offset integer

In this table, only two attributes are considered which are user_time_zone and user_utc_offset. In a general real-world scenario, the user_location might also be considered but as per the given data, the location is given something as a fake value like in my home, in my room etc which does not matches with the real-world location and hence the user_location is not added in here. For example, in a bigger scenario, an ideal design might include location, time_zone and utc_offset in a single table since the time_zone and utc_offset depends on the location

## USER_DETAILS:
- user_id bigint primary key,
- user_name character varying (255),
- user_screen_name character varying (255),
- user_location character varying (255),
- user_time_zone character varying (127),
- user_followers_count integer,
- user_friends_count integer,
- user_lang character varying (10), 
- user_description varchar (255),
- user_status_count varchar (255), 
- user_created_at timestamp with time zone,
- foreign key(user_time_zone) references user_time_zone_offset(user_time_zone) 

In this table, user_id is the primary key and user_time_zone is the foreign key referencing user_time_zone from the user_time_zone_offset table. All the other attributes like user_name, user_screen_name, user_location, user_followers_count, user_friends_count, user_lang, user_description, user_status_count, user_created_at are specifically related to a particular user and can be identified with the user_id. The relationship is that there’s always only one time_zone is available for a single user.

## TWEETS:
- tweet_id bigint primary key,
- text varchar (255),
- created_at timestamp with time zone,
- in_reply_to_screen_name varchar(255),
- in_reply_to_status_id bigint,
- in_reply_to_user_id bigint,
- retweet_count int, 
- tweet_source varchar (255),
- user_id bigint,
- foreign key(user_id) references user_details(user_id)

In this table, tweet_id is the primary key and user_id is the foreign key which references the user_id from the user_details table. All the other attributes like text, created_at, in_reply_to_screen_name, in_reply_to_status_id, in_reply_to_user_id, retweet_count, tweet_source is specifically related to a particular tweet and can be identified with the tweet_id. In a usual real-world scenario, the tweet_source can be generalized for example, if there’s more information about the type of device that a particular person is using to tweet like a mobile or a laptop and depending on the source, this attribute can be aligned with another table having attributes as device type and tweet_source but for the data given, I’ve inserted tweet_source as an attribute in this table itself.

## HASHTAGS:
- tweet_id bigint,
- hashtags varchar (144),
- primary key(tweet_id, hashtags),
- foreign key(tweet_id) references original_tweets

The relationship between hashtags and original_tweets table is many to one i.e., many hashtags may be related to one tweet.

FEW queries related to fetching the required data from tables:

How are these tweets distributed across languages?
Write a query that shows, for every language (user_lang) the number of tweets in that language.

select user_lang,sum(counts) as total_tweets from (
select user_lang,count(tweet_id) as counts from original_tweets ot join 
user_details ud on ot.user_id=ud.user_id
group by ud.user_lang
union all
select user_lang,count(tweet_id) as counts from retweeted_tweets rt join 
user_details ud on rt.user_id=ud.user_id
group by ud.user_lang
) as total_language_counts
group by total_language_counts.user_lang
order by total_tweets desc;


