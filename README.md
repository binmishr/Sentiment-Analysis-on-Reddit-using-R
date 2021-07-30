# Sentiment-Analysis-on-Reddit-using-R

According to Wikipedia, Reddit is an American social news aggregation, web content rating, and discussion website. Registered members submit content to the site such as links, text posts, images, and videos, which are then voted up or down by other members. Posts are organized by subject into user-created boards called “communities” or “subreddits”, which cover a variety of topics such as news, politics, religion, science, movies, video games, music, books, sports, fitness, cooking, pets, and image-sharing.

According to me, Reddit is almost anonymous social media platform that is out there. Most users don’t even know each other in real life. But, a lot of conversations and engagement happens from users across the world.

I am not going to go in detail on how to do sentiment analysis but will link my github repo and my binder link to run my code.
Why sentiment analysis on reddit is good?

With a lot of users and a lot of engagement, it’s a great platform to extract individual comments on an asset, topic, company and perform analytics on it. Being anonymous also psychologically help users to speak what’s on their mind without repercussions. This helps in getting more accurate information.
Why sentiment analysis on reddit is bad?

Firstly, not all users engage in discussion on a topic or a post. In pure reddit terms, they are called as lurkers. With a lot of lurkers on reddit, it’s hard to estimate true sentiment.

Secondly, there are a lot of bots on reddit. Not just on reddit but on most platforms like Twitter and Facebook. With a lot of bots, you can definitely skew the sentiment on a post. Hence, any sentiment here must be taken with a grain of salt.
Sentiment analysis in R on a Reddit post

To perform sentiment analysis, first we need to extract the comments on a topic. RedditExtractorR is a great package. No API Auth keys required like it’s counter parts twitterR.

# Getting Reddit 
Datalinks = reddit_urls(search_terms = "Amazon", page_threshold = 50)
links = links[links$num_comments>5,]

Next, we need to parse through all the links and extract information and perform sentiment analysis and bind the results of each link.

  
# function to iterate through all posts
funct = function(i){ 
content = reddit_content(links$URL[i]) 
com = iconv(content$comment, to = 'utf-8') 
clov = get_nrc_sentiment(com) 
x1 = 100*colSums(clov)/sum(clov) 
return(cbind(links[i,], t(x1) ))} 

# list of all the links
ls = 1:nrow(links) 

# loop through all the links and bind to a data frame
res = do.call("rbind", lapply(ls, funct))
res$date = as.Date(res$date, "%d-%m-%y")

We will arrive at a table with all the input data and all the sentiment scores. Let’s assume, we just need to know if it’s a positive or a negative sentiment in time series. To do this we will aggregate data by month and then summarize by using dplyr.

 # aggregate data by month
res$month = floor_date(res$date, "month") 

# summarize all results
xx = res %>% group_by(month) %>% summarise(positive

Finally we can plot the results using a nice bar plot showing trends of positive or negative sentiment.

 # plot results
barplot(xx[,2:3] %>% as.matrix() %>% t(), col=c("green","red"), main = "Sentiment history", names.arg = xx$month, xlab = "Month", ylab = "Sentiment")

This one simple way to do a quick Sentiment analysis in a particular topic on reddit using R.
