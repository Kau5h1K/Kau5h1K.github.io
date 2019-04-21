---
layout: post
title: "Game of Thrones analysis with Networks"
date: 2019-04-21
tags: [ Project, python, networkx, pagerank, betweenness_centrality, degree_centrality]
excerpt: "Jon Snow, Daenerys Targaryen, or Tyrion Lannister? Who is the most important character in Game of Thrones? Let's see what mathematics can tell us about this!
In this project, I look at the character co-occurrence network and its evolution over the five books in R.R. Martin's hugely popular book series A Song of Ice and Fire (perhaps better known as the TV show Game of Thrones). I also look at how the importance of the characters changes over the books using different centrality measures."
comments: True
project: True
---
If you haven't heard of Game of Thrones, then you must be really good at hiding. Game of Thrones is the hugely popular television series by HBO based on the (also) hugely popular book series A Song of Ice and Fire by George R.R. Martin. In this notebook, I will analyze the co-occurrence network of the characters in the Game of Thrones books. Here, two characters are considered to co-occur if their names appear in the vicinity of 15 words from one another in the books.

Image

Data

This dataset constitutes a network and is given as a text file describing the edges between characters, with some attributes attached to each edge. Let's start by loading in the data for the first book A Game of Thrones and inspect it.

import pandas as pd
# Reading in datasets/book1.csv
book1 = pd.read_csv("datasets/book1.csv")

# Printing out the head of the dataset
book1.head()
