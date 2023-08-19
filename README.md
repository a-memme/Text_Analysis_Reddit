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


## Results 
### Extraction 



content lanes and topics that are performing well at any given moment is crucial when looking to produce and redistribute content, as well as 
