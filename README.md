

# Text Summarization with Web Scraping

## Overview

This Python script performs text summarization using web scraping techniques to extract information from a Wikipedia article on Artificial Intelligence. The summarization process involves importing libraries, fetching data from the web, cleaning the text, and scoring sentences based on word frequency.

## Prerequisites

- Python 3.x
- Install required libraries:

  ```bash
  pip install -r requirements.txt
  ```

## Import Libraries

```python
# Beautiful Soup for web scraping (pulling data from HTML and XML files)
import bs4 as bs

# For opening and reading URL.
import urllib.request

import re  # Regular expression
import nltk  # Natural Language Toolkit

nltk.download('punkt')
nltk.download('stopwords')
```

## Import Data

```python
# Open the URL and retrieve the content from it
scraped_data = urllib.request.urlopen('https://en.wikipedia.org/wiki/Artificial_intelligence')

# Read the HTML content from the web page
article = scraped_data.read()

# Convert data to text (XML) 
parsed_article = bs.BeautifulSoup(article, 'lxml')

paragraphs = parsed_article.find_all('p')

# Collect all text in one variable
article_text = ""

for p in paragraphs:
    article_text += p.text
```

## Text Cleaning

```python
# Remove numerical references
article_text = re.sub(r'\[[0-9]*\]', ' ', article_text)
article_text = re.sub(r'\s+', ' ', article_text)

# Remove non-alphabetic characters
formatted_article_text = re.sub('[^a-zA-Z]', ' ', article_text)
formatted_article_text = re.sub(r'\s+', ' ', formatted_article_text)
```

## Tokenization and Removing Stop Words

```python
# Tokenize sentences
sentence_list = nltk.sent_tokenize(article_text)

# Tokenize words and remove stop words
stopwords = nltk.corpus.stopwords.words('english')
word_frequencies = {}

for word in nltk.word_tokenize(formatted_article_text):
    if word not in stopwords:
        if word not in word_frequencies.keys():
            word_frequencies[word] = 1
        else:
            word_frequencies[word] += 1
```

## Normalization

```python
# Calculate the frequency of each word
maximum_frequency = max(word_frequencies.values())

for word in word_frequencies.keys():
    word_frequencies[word] = (word_frequencies[word] / maximum_frequency)
Calculate Sentence Scores

# Calculate the sentence score: how many times a word appears in the sentence
sentence_scores = {}

for sent in sentence_list:
    for word in nltk.word_tokenize(sent.lower()):
        if word in word_frequencies.keys():
            if len(sent.split(' ')) < 30:
                if sent not in sentence_scores.keys():
                    sentence_scores[sent] = word_frequencies[word]
                else:
                    sentence_scores[sent] += word_frequencies[word]
```

## Extract Top Sentences

```python
# Extract the top N sentences with the highest scores
import heapq

summary_sentences = heapq.nlargest(7, sentence_scores, key=sentence_scores.get)

summary = ' '.join(summary_sentences)

print(summary)
```

## Usage

Run the script to fetch data from the specified Wikipedia article, clean the text, and generate a summary of the content.

```bash
python main.py
```



## Acknowledgments

- [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/)
- [NLTK](https://www.nltk.org/)

Feel free to contribute, report issues, or suggest improvements! Happy coding!

--- 

