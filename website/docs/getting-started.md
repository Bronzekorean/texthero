---
id: getting-started
title: Getting started
---

## <span style="color: #ff8c42">Texthero</span> from <span style="color: #ff8c42">zero</span> to <span style="color: #ff8c42">hero</span>.

Texthero is a python package to let you work efficiently and quickly with text data. You can think of texhero as _scikit-learn for text-based dataset_.

## Overview

Given a dataset with structured data, it's easy to have a quick understanding of the underline data. Oppositely, given a dataset composed of text-only, it's harder to have a quick undertanding of the data. Texthero help you there, providing utility functions to quickly **clean the text data**, **map it into a vector space** and gather from it **primary insights**.

##### Pandas integration

One of the main pillar of texthero is that is designed from the ground-up to work with **Pandas Dataframe** and **Series**.

Most of texthero methods, simply apply transformation to Pandas Series. As a rule of thumb, the first argument and the return ouputs of almost all texthero methods are either a Pandas Series or a Pandas DataFrame.


##### Pipeline

The first phase of almost any natural language processing is almost the same, independently to the specific task.

Pandas introduced Pipe function starting from version 0.16.2. Pipe enables user-defined methods in method chains

## Installation and import

Texthero is available on <a href="https://pypi.org/project/texthero/" target="\_blank">pip</a>. To install it open a terminal and execute

```bash
pip install texthero
```

If you have already installed it and want to upgrade to the last version type:

```bash
pip install texthero -U
```

## Getting started

For a simple example we make use of the [BBC Sport Dataset](http://mlg.ucd.ie/datasets/bbc.html), it consists of 737 documents from the BBC Sport website corresponding to sports news articles in five topical areas from 2004-2005.

The five different areas are _athletics_, _cricket_, _football_, _rugby_ and _tennis_.

The original dataset comes as a zip files with five different folder containing the article as text data for each topic.

For convenience, we createdThis script simply read all text data and store it into a Pandas Dataframe.

Import texthero and pandas.

```python
import texthero as hero
import pandas as pd
```

Load the `bbc sport` dataset in a Pandas DataFrame.

```python
pd = pd.read_csv(
   "https://github.com/jbesomi/texthero/raw/master/dataset/bbcsport.csv"
)
```

```python
>>> df.head(2)
                                               text     topic
0  "Claxton hunting first major medal\n\nBritish h..."  athletics
1  "O'Sullivan could run in Worlds\n\nSonia O'Sull..."  athletics
```


### Preprocessing

##### Clean

To clean the text data all we have to do is:

```python
df['text_clean'] = hero.clean(df['text'])
```

Recently, Pandas has introduced the pipe function. You can achieve the same results with

```python
df['text_clean'] = df['text'].pipe(hero.clean)
```

The default pipeline for the clean method is:

1. `preprocessing.fillna(s)` Replace not assigned values with empty spaces.
1. `preprocessing.lowercase(s)` Lowercase all text.
1. `preprocessing.remove_digits()` Remove all blocks of digits.
1. `preprocessing.remove_punctuation()` Remove all string.punctuation (!"#$%&\'()\*+,-./:;<=>?@[\\]^\_\`{|}~).
1. `preprocessing.remove_diacritics()` Remove all accents from strings.
1. `preprocessing.remove_whitespace()` Remove all white space between words.

> As texthero is still in beta, the default pipeline may undergo some minor changes in the next versions.

##### Custom pipeline

We can also pass a custom pipeline as argument to `clean`

```python
from texthero import preprocessing

custom_pipeline = [preprocessing.fillna,
                   preprocessing.lowercase,
                   preprocessing.remove_whitespaces]
df['clean_text'] = hero.clean(df['text'])
```

or alternatively

```python
df['clean_text'] = df['clean_text']).pipe(hero.clean, custom_pipeline)
```

##### Preprocessing API

The complete preprocessing API can be found at the following address: [api preprocessing](/docs/api-preprocessing).


### Representation

Once cleaned the data, the next natural is to map each document into a vector.

##### TFIDF representation


```python
df['tfidf_clean_text'] = hero.do_tfidf(df['clean_text'])
```

##### Dimensionality reduction with PCA

To visualize the data, we map each point to a two-dimensional representation with PCA. The principal component analysis algorithms returns the combination of attributes that better account the variance in the data.

```python
df['pca_tfidf_clean_text'] = hero.do_pca(df['tfidf_clean_text'])
```

##### All in one step

We can achieve all the three steps show above, _cleaning_, _tf-idf representation_ and _dimensionality reduction_ in a single step. Isn't fabulous?

```python
df['pca'] = (
            df['text']
            .pipe(hero.clean)
            .pipe(hero.do_tfidf)
            .pipe(hero.do_pca)
   )
```

##### Representation API

The complete representation module API can be found at the following address: [api representation](/docs/api-representation).

### Visualization

`texthero.visualization` provide some helpers functions to visualize the tarnsformed Dataframe. All visualization utilize under the hoods the [Plotly Python Open Source Graphing Library](https://plotly.com/python/).


```python
hero.scatterplot(df, col='pca', color='labels', title="PCA BBC Sport news")
```

![](/img/scatterplot_bccsport.svg)

Also, we can "visualize" the most common words for each `topic` with `top_words`

```python
NUM_TOP_WORDS = 5
df.groupby('topic')['text'].apply(lambda x: hero.top_words(x)[:NUM_TOP_WORDS])
```

```
topic             
athletics  said       0.010068
           world      0.008900
           year       0.008844
cricket    test       0.008250
           england    0.008001
           first      0.007787
football   said       0.009515
           chelsea    0.006110
           game       0.005950
rugby      england    0.012602
           said       0.008359
           wales      0.007880
tennis     6          0.021047
           said       0.013012
           open       0.009834
```


##### Visualization API

The complete visualization module API can be found at the following address: [api visualization](/docs/api-visualization).

## Summary

We saw how in just a couple of lines of code we can represent and visualize any text dataset. We went from knowing nothing regarding the dataset to see that there are 5 (quite) distinct areas representig each topic. We went _from zero to hero_.

```python
import texthero as hero
import pandas as pd

df = pd.read_csv(
    "https://github.com/jbesomi/texthero/raw/master/dataset/bbcsport.csv"
)
df['pca'] = (
    df['text']
    .pipe(hero.clean)
    .pipe(hero.do_tfidf)
    .pipe(hero.do_pca)
)

hero.scatterplot(df, col='pca', color='topic', title="PCA BBC Sport news")
```

##### Next section

By now, you should have understood the main building blocks of texthero.

In the next sections, we will review each module, see how we can tune the default settings and we will show other application where Texthero might come in handy.
