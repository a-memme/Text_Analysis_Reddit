# Keyword Extraction and Topic Analysis - Reddit
*Analyzing top performing keywords and topics from a popular subreddit thread, utilizing a variety of NLP techniques.*

## Purpose
In the online media industry, understanding the best performing content lanes under whichever platform/medium channel of interest is valuable knowledge when considering content creation and redistribution. Furthermore, continuously keeping one's finger on the pulse as to how topics of interest may change at any given time is critical when looking to maintain an agile, sustainable, and effective content strategy. 

The current analysis leverages text analysis - particularily keyword extraction and correlation - to understand the most popular and top performing keywords of a popular subreddit thread, filtered by recency. By leveraging keyword correlaton - i.e understanding how said keywords of interest relate to one another - subcategories or subtopics can then be deduced from the information available, providing a richer understanding of content lane performance.

## Method
### Overview
- Access Reddit data at scale via the Python Reddit API Wrapper library (PRAW).
- Analyze reddit post titles via 2 competing methods and assess the ouputs of each:
    - Standard tokenization + lemmatization
    - KeyBERT keyword extraction model (see reference for more details).
- Calculate average metric scores of keywords outputted by the model
- Calculate a correlation matrix of the keywords to determine a richer understanding of top performing keywords and topics

### Data Extraction 
*See ipynb page for details*
- PRAW library
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
### Tokenization vs KeyBERT
- **Tokenization + Lemmatization**
    - Subreddit post titles are broken down via tokenization, lemmatization, and filtered further by removing both stopwords and non-alpha characters, with a limit of 3 letters per word (See lines ... for code)
    - Lemmatization is preferred over stemming here as we're looking for the dictionary-based morphological root of the words of interest rather than the base root - the dictionary-based representation is typically easier for general interpretation and more suitable for this analytical case.
 
- **KeyBERT**
    - The KeyBERT technique is an easy to use framework, leveraging BERT embeddings - i.e a bi-directional transformer model utilizing semantic similarity for keywords and phrases - to extract said keywords or phrases from pieces of text.
    - Here we specify our extraction technique based on a number of factors:
        -  N-gram range of 1,1 (i.e singular words instead of bi or tri-grams).
        -  Removing stopwords (similar to above).
        -  Maximal Marginal Relevance
            -  Leveraging cosine similarity to first, find keywords with maximum relevance to the entire text and second, to iteratively choose new cadidates that are both similar to the text and not similar to the rest of the chosen keywords
        -  Experimenting with ranges of keywords to include - a large value is used to include all keywords captured.

## Results 
### Keyword Extraction
- Both techniques appear to output very similar results, often seeing a greater inclusion of words in the tokenizing/lemmatizing approach
    - For this analysis, the tokenization/lemmatization approach is chosen as it appears to cast a slightly "wider net" in capturing keywords while still providing us with the confidence that irrelevant keywords are not diluting the mix in comparison to the KeyBERT approach
    - Although KeyBERT provides a very sensible approach to typical keyword extraction, its level of sophistication is simply not needed in our specific use case - see below:
    - **Tokenization/Lemmatization**
    
        ![image](https://github.com/a-memme/Text_Analysis_Reddit/assets/79600550/9bb8cd2f-331b-46a2-b70c-ff6c4ebc3481)
    
    - **KeyBERT**

        ![image](https://github.com/a-memme/Text_Analysis_Reddit/assets/79600550/896d048b-4a83-41e8-bba1-68b7c36f22ca)

- Because determining stopwords is somewhat of a subjective approach, we can identify frequently appearing stopwords that were missed in the first step and omit them from the analysis moving forward:
```
#Store keywords and popularity from token set in a df - token set used to cast a wider net in this case (more flexibility)
popular_words = keyword_freq('tokens')

#Only keep keywords that appear 20 more times - manually remove stopwords that weren't caught by the nltk stopwords library
popular_cats_set = set(popular_words[(popular_words.counts>=20)]['flat_categories'])
popular_cats_set.difference_update(['want', 'let', 'ask', 'get', 'make', 'tell', 'take', 'told', 'say'])
```

### Keyword Performance 
- Top keywords can be assessed by score, number of comments, upvote ratio, and frequency - see below:
#### Score
![image](https://github.com/a-memme/Text_Analysis_Reddit/assets/79600550/137619b6-50f2-4dfb-b33e-78d3dc9a4383)

#### Upvote Ratio
![image](https://github.com/a-memme/Text_Analysis_Reddit/assets/79600550/137a83a9-ff9c-4c0e-9c5a-5d72893f09d3)

#### Comments
![image](https://github.com/a-memme/Text_Analysis_Reddit/assets/79600550/867b796c-0c5f-46a9-9771-e1ae1ee46a39)

### Keyword Correlation 
- By transforming the popular keyword dataframe into a vector matrix, we are able to calculate/visualize the correlations between them, enriching our analysis of high performing and popular keywords/topics - see below:
![image](https://github.com/a-memme/Text_Analysis_Reddit/assets/79600550/77fff600-f1af-4f0a-b71a-ef60ffaa06ec)

## Discussion 
### Individual Keyword Performance 
- By assessing words that appear in multiple top lists, we can easily infer high-level topics (or at the least, keywords) that perform well on the subreddit.
- Overall, we can see that most words are relevant to friends & family drama. More specific topics of interest may include:
    - dinner (top score & comments)
    - weddings (top score, upvote and popularily used)
    - paying (in all top lists & popularily used)
    - etc...

### Keyword Relevance 
- Although keyword performance may seem broad and simple, by assessing the correlations between said words (i.e how often do they appear or not appear together), we can enrichen our understandings of said topics. For instance:
    - Example 1: Weddings
        - Strong correlations with gifts, brothers/sisters, and strongest correlation to inviting/invitations (lemmatized word = 'invite')
        - Here we can infer that a lot of the popular/high performing wedding content may be dealing with drama specifically around a sibling's wedding, gifts at a wedding, or drama around invites/types of invitations etc.
        - One step further could look at multicolinearity - that is, do we see relationships between gifts and siblings? (we don't - this would indicate that high performance in those categories are likely seperate from each other, i.e weddings + siblings OR weddings + gifts but not all three inclusive).
    - Example 2: Paying  
        - Correlations with vacations, refusals, boyfriends and family.
        - Refusal provides very powerful context to the popularity of paying - i.e high performing content surrounding the refusal to pay for things - and unlike the example above, we can see that boyfriends are both strongly correlated to paying AND refusals, providing an even deeper context of a high performing content lane that includes stories of boyfriends refusing to pay for things.

## Conclusion
The current analysis looks to provide value to understanding online media content performance via popuarily used NLP techniques, implemented in a simple yet powerful approach. Furthermore, domain knowledge - as it often does - becomes extremely important when considering model & parameter selection, as well as final analysis and interpretation of the results. Please reach out for any comments, questions or suggestions!

## References
- KeyBERT:
    - https://github.com/MaartenGr/KeyBERT
    - https://towardsdatascience.com/keyword-extraction-with-bert-724efca412ea
- PRAW library:
    - https://github.com/praw-dev/praw
