# Keyword Extraction and Topic Analysis - Reddit
*Analyzing top performing keywords and topics of a popular subreddit through use of a variety of NLP techniques.*

## Purpose
In the online media industry, understanding the best performing content lanes under whichever platform/medium channel of interest is valuable knowledge when considering content creation and redistribution. Furthermore, continuously keeping one's finger on the pulse as to how topics of interest may change at any given time is critical when looking to maintain an agile, sustainable, and effective content strategy. 

The current analysis leverages text analysis - particularily keyword extraction and correlation - to understand the most popular and top performing keywords of a popular subreddit thread, filtered by recency. By leveraging keyword correlaton - i.e understanding how said keywords of interest relate to one another - subcategories or subtopics can then be deduced from the information available, providing a richer understanding of content lane performance.

## Method
### Overview
- Access Reddit data at scale via the Python Reddit API Wrapper library (PRAW).
- Analyze reddit post titles via 2 competing methods and assess the ouputs of each:
    - Standard lemmatization + tokenization
    - KeyBERT algorithm (see reference below)
- Calculate average metric scores of keywords outputted by the model
- Calculate a correlation matrix of the keywords to determine a richer understanding of top performing keywords and topics

### Data Extraction 
*See ipynb page for details*
- Using the PRAW library, API connection can be configured quite easily (see below).
- Using the top() function with parameters, the top 1000 posts within the year are accessed, looping through each post to get metrics of interest - see code below:
```
#Function for retrieving top posts
def get_posts(subreddit_name):

  #Lists
  title_list = []
  score_list = []
  num_comments_list = []
  url_list = []
  up_list = []
  down_list = []
  upvote_ratio_list = []

  subreddit = reddit.subreddit(subreddit_name)
  # Get the top posts from the subreddit - can toggle between top vs hot option (for our case, we want to cast a larger net and therefore go for top)
  top_posts = subreddit.top(limit=1000, time_filter='year')
  #top_posts = subreddit.hot(limit=1000)

  # Process top posts
  for post in top_posts:
    # Accessing different metrics of interest
    title = post.title
    score = post.score
    num_comments = post.num_comments
    upvote_ratio = post.upvote_ratio
    ups = post.ups
    downs = post.downs

    # post conditionals
    if score >= 10000 and num_comments >= 100:
      title_list.append(title)
      score_list.append(score)
      num_comments_list.append(num_comments)
      url_list.append(post.url)
      up_list.append(ups)
      down_list.append(downs)
      upvote_ratio_list.append(upvote_ratio)

  
  #Create dataframe
  aita_df = pd.DataFrame({'post': title_list,
                         'score': score_list,
                         'number_of_comments': num_comments_list,
                         'url': url_list,
                         "ups": up_list,
                         "downs": down_list,
                         'upvote_ratio': upvote_ratio_list
                         })
  
  return aita_df
```

## Results 
### Tokenization vs KeyBERT



content lanes and topics that are performing well at any given moment is crucial when looking to produce and redistribute content, as well as 
