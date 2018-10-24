# We Rate Dogs – Wrange Report.

## Author: Ram Saran Vuppuluri

In this report we are going to analyze We Rate Dogs to answer following questions:

1.	Rating frequency
2.	Dog type frequency
3.	Relationship between Dog Type and Favorite count.
4.	Relationship between Dog Type and Retweet count.
5.	Relationship between Rating and Favorite count.
6.	Relationship between Rating and Retweet count.

Before analyzing the data, we need to perform following 3 steps:
1.	Gather data from one or more data sources.
2.	Assess data for any quality or tidiness issues.
3.	Clean data of the quality, tidiness issues identified during asses data step.

## Gather Data

As per the problem statement we need to gather data from three data sources.
1.	__twitter-archive-enhanced.csv__ file provided by Udacity. This file is available for download directly. Once the file is downloaded it will be placed in the current working directory of the notebook.
2.	__[image_prediction.tsv]( https://d17h27t6h515a5.cloudfront.net/topher/2017/August/599fd2ad_image-predictions/image-predictions.tsv)__ file is provided by Udacity. This file is not directly available for download. Instead we need to utilize Python “requests” package.
3.	__tweet_json.txt__ file. This file is constructed by using tweet_id’s from twitter-archive-enhanced.csv file. We use “tweepy” library as API to retrieve data for each of tweet_id’s.
To connect to Twitter via Tweepy API, we need to perform following steps for a new Twitter user:
    1.	Create a regular Twitter account.
    2.	Once the regular Twitter account is created, create a developer account.
    3.	Create an application on Twitter developer dashboard.
    4.	New application will generate:
        * Consumer API key
        * Consumer API secret key
        * Access token key
        * Access token secret key


Once app authentication keys are available we can connect to Twitter via Tweepy API.

We will consolidate and load the data into 1 master Pandas dataframe.

## Assess Data

Now that we have gathered and loaded data into Pandas dataframes, we will perform visual and programmatic assessment.

On visual assessment 

1. Following columns are loaded as int64 instead of String (object).
    * tweet_id
    * id_str
2. Following columns are loaded as float instead of String (object).    
    * in_reply_to_status_id_x
    * in_reply_to_user_id_x
    * retweeted_status_id
    * retweeted_status_user_id
    * in_reply_to_status_id_y
    * in_reply_to_status_id_str
    * in_reply_to_user_id_y
    * in_reply_to_user_id_str
    * quoted_status_id
    * quoted_status_id_str
3. There are missing values in following columns:
    * in_reply_to_status_id_x
    * in_reply_to_user_id_x
    * retweeted_status_id
    * retweeted_status_user_id
    * retweeted_status_timestamp
    * expanded_urls
    * contributors
    * coordinates
    * geo
    * in_reply_to_screen_name
    * in_reply_to_status_id_y
    * in_reply_to_status_id_str
    * in_reply_to_user_id_y
    * in_reply_to_user_id_str
    * place
    * quoted_status
    * quoted_status_id
    * quoted_status_id_str
    * quoted_status_permalink
    * retweeted_status
4. Following columns are loaded as String (object) instead of timestamp instance.
    * timestamp
    * retweeted_status_timestamp
5. "lang" column is loaded as String (object) instead of catergory.
6. "expanded_urls" column contains more than 1 URL. (This obeservation is made by examining the values in Excel).
7. Following columns are common in between twitter_arch_df and tweet_json_df, we can remove duplicate columns and rename the columns existing columns.
    * in_reply_to_status_id
    * in_reply_to_user_id
    * source

Pandas Dataframe describe() will provide basic statistical analysis of numerical data in dataframe.

On Programmatically assessment 
1. source_x, source_y has only 3 unique values each. We can ignore this column.
2. Dog names identified in p1,p2 and p3 columns contain special characters "-","_".
3. There is huge variation between 75th percentile of rating_numerator and maximum value.
4. rating_denominator contains values other than 10.

From above visual and programmatic analysis, we find quality and tidiness issues:

#### Quality issues:

1. Following columns are loaded as int64 instead of String (object).
    * tweet_id
    * id_str
2. Following columns are loaded as float instead of String (object), we can remove these columns as they are not utilized in analysis.    
    * in_reply_to_status_id_x
    * in_reply_to_user_id_x
    * retweeted_status_id
    * retweeted_status_user_id
    * in_reply_to_status_id_y
    * in_reply_to_status_id_str
    * in_reply_to_user_id_y
    * in_reply_to_user_id_str
    * quoted_status_id
    * quoted_status_id_str
3. There are missing values in following columns, we can remove these columns as they are not utilized in analysis.
    * retweeted_status_timestamp
    * expanded_urls
    * contributors
    * coordinates
    * geo
    * in_reply_to_screen_name
    * place
    * quoted_status
    * quoted_status_permalink
    * retweeted_status
4. Following columns are loaded as String (object) instead of datetime64 instance.
    * timestamp
5. "lang" column is loaded as String (object) instead of catergory.
6. "expanded_urls" column contains more than 1 URL. (This obeservation is made by examining the values in Excel). We can remove this column as it is not utilized in analysis.
7. rating_numerator should be in range 10 to 14, but there are values out of range. We can remove out of range values.
8. rating_denominator should be 10, there are values not equal to 10. We can remove non 10 values.

#### Tidiness issues:

1. Following columns are common in between twitter_arch_df and tweet_json_df, we can remove duplicate columns and rename the columns existing columns.
    * in_reply_to_status_id
    * in_reply_to_user_id
    * source
2. Following columns are not needed for analysis.
    * timestamp
    * source_x
    * text
    * name                           
    * jpg_url
    * img_num
    * created_at
    * display_text_range
    * entities
    * extended_entities                   
    * favorited
    * full_text
    * id_str
    * is_quote_status
    * lang
    * possibly_sensitive
    * possibly_sensitive_appealable    
    * retweeted
    * source_y
    * truncated
    * user
3. Not all records identified in image-prediction file not always dogs. We can remove non dog records.
4. Image prediction data has 3 possible predictions for each of the tweets along with whether the prediction has classified as dog or not and confidence of the predictions. We will 
    1. use only predictions that are of dogs.
    2. select maximum prediction value.
    3. use the dog breed from maximum prediction value.
5. Dog breeds contain special characters "-","_". We will replace the special characters with " ".

## Clean Data

We will create a new copy of consolidated data frame and apply the quality and tidiness issues identified in the Assess Data stage.
