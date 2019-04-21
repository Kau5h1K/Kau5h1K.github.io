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
## Data
---
This project uses a dataset parsed by Andrew J. Beveridge and Jie Shan which is available [here](https://github.com/Kau5h1K/network-of-thrones/tree/master/datasets). For more information on this dataset have a look at the Network of Thrones [blog](https://networkofthrones.wordpress.com/).

---
## 1. Winter is Coming. Let's load the dataset ASAP!
<p>If you haven't heard of <em>Game of Thrones</em>, then you must be really good at hiding. Game of Thrones is the hugely popular television series by HBO based on the (also) hugely popular book series <em>A Song of Ice and Fire</em> by George R.R. Martin. In this notebook, we will analyze the co-occurrence network of the characters in the  Game of Thrones books. Here, two characters are considered to co-occur if their names appear in the vicinity of 15 words from one another in the books. </p>
<p><img src="https://assets.datacamp.com/production/project_76/img/got_network.jpeg" style="width: 550px"></p>
<p>This dataset constitutes a network and is given as a text file describing the <em>edges</em> between characters, with some attributes attached to each edge. Let's start by loading in the data for the first book <em>A Game of Thrones</em> and inspect it.</p>


```python
# Importing modules
import pandas as pd
# Reading in datasets/book1.csv
book1 = pd.read_csv("datasets/book1.csv")

# Printing out the head of the dataset
book1.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Source</th>
      <th>Target</th>
      <th>Type</th>
      <th>weight</th>
      <th>book</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Addam-Marbrand</td>
      <td>Jaime-Lannister</td>
      <td>Undirected</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Addam-Marbrand</td>
      <td>Tywin-Lannister</td>
      <td>Undirected</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Aegon-I-Targaryen</td>
      <td>Daenerys-Targaryen</td>
      <td>Undirected</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aegon-I-Targaryen</td>
      <td>Eddard-Stark</td>
      <td>Undirected</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aemon-Targaryen-(Maester-Aemon)</td>
      <td>Alliser-Thorne</td>
      <td>Undirected</td>
      <td>4</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>


---
## 2. Time for some Network of Thrones
<p>The resulting DataFrame <code>book1</code> has 5 columns: <code>Source</code>, <code>Target</code>, <code>Type</code>, <code>weight</code>, and <code>book</code>. Source and target are the two nodes that are linked by an edge. A network can have directed or undirected edges and in this network all the edges are undirected. The weight attribute of every edge tells us the number of interactions that the characters have had over the book, and the book column tells us the book number.</p>
<p>Once we have the data loaded as a pandas DataFrame, it's time to create a network. We will use <code>networkx</code>, a network analysis library, and create a graph object for the first book.</p>


```python
# Importing modules
import networkx as nx
# Creating an empty graph object
G_book1 = nx.Graph()
```
---
## 3. Populate the network with the DataFrame
<p>Currently, the graph object <code>G_book1</code> is empty. Let's now populate it with the edges from <code>book1</code>. And while we're at it, let's load in the rest of the books too!</p>


```python
# Iterating through the DataFrame to add edges
for _,edge in book1.iterrows():
    G_book1.add_edge(edge['Source'],edge['Target'],weight=edge['weight'])
# Creating a list of networks for all the books
books = [G_book1]
book_fnames = ['datasets/book2.csv', 'datasets/book3.csv', 'datasets/book4.csv', 'datasets/book5.csv']
for book_fname in book_fnames:
    book = pd.read_csv(book_fname)
    G_book = nx.Graph()
    for _, edge in book.iterrows():
        G_book.add_edge(edge['Source'], edge['Target'], weight=edge['weight'])
    books.append(G_book)
```
---
## 4. The most important character in Game of Thrones
<p>Is it Jon Snow, Tyrion, Daenerys, or someone else? Let's see! Network science offers us many different metrics to measure the importance of a node in a network. Note that there is no "correct" way of calculating the most important node in a network, every metric has a different meaning.</p>
<p>First, let's measure the importance of a node in a network by looking at the number of neighbors it has, that is, the number of nodes it is connected to. For example, an influential account on Twitter, where the follower-followee relationship forms the network, is an account which has a high number of followers. This measure of importance is called <em>degree centrality</em>.</p>
<p>Using this measure, let's extract the top ten important characters from the first book (<code>book[0]</code>) and the fifth book (<code>book[4]</code>).</p>


```python
# Calculating the degree centrality of book 1
deg_cen_book1 = nx.degree_centrality(books[0])

# Calculating the degree centrality of book 5
deg_cen_book5 = nx.degree_centrality(books[4])

# Sorting the dictionaries according to their degree centrality and storing the top 10
sorted_deg_cen_book1 = sorted(deg_cen_book1.items(),key=lambda kv:kv[1],reverse=True)[:10]
#import operator
#sorted_deg_cen_book1 = sorted(deg_cen_book1.items(),key=operator.itemgetter(1),reverse=True)[:10]
# Sorting the dictionaries according to their degree centrality and storing the top 10
sorted_deg_cen_book5 = sorted(deg_cen_book5.items(),key=lambda kv:kv[1],reverse=True)[:10]

# Printing out the top 10 of book1 and book5
print(sorted_deg_cen_book1)
print(sorted_deg_cen_book5)
```

    [('Eddard-Stark', 0.3548387096774194), ('Robert-Baratheon', 0.2688172043010753), ('Tyrion-Lannister', 0.24731182795698928), ('Catelyn-Stark', 0.23118279569892475), ('Jon-Snow', 0.19892473118279572), ('Sansa-Stark', 0.18817204301075272), ('Robb-Stark', 0.18817204301075272), ('Bran-Stark', 0.17204301075268819), ('Cersei-Lannister', 0.16129032258064518), ('Joffrey-Baratheon', 0.16129032258064518)]
    [('Jon-Snow', 0.1962025316455696), ('Daenerys-Targaryen', 0.18354430379746836), ('Stannis-Baratheon', 0.14873417721518986), ('Theon-Greyjoy', 0.10443037974683544), ('Tyrion-Lannister', 0.10443037974683544), ('Cersei-Lannister', 0.08860759493670886), ('Barristan-Selmy', 0.07911392405063292), ('Hizdahr-zo-Loraq', 0.06962025316455696), ('Asha-Greyjoy', 0.056962025316455694), ('Melisandre', 0.05379746835443038)]

---
## 5. The evolution of character importance
<p>According to degree centrality, the most important character in the first book is Eddard Stark but he is not even in the top 10 of the fifth book. The importance of characters changes over the course of five books because, you know, stuff happens... ;)</p>
<p>Let's look at the evolution of degree centrality of a couple of characters like Eddard Stark, Jon Snow, and Tyrion, which showed up in the top 10 of degree centrality in the first book.</p>


```python
%matplotlib inline

# Creating a list of degree centrality of all the books
evol = [nx.degree_centrality(book) for book in books]

# Creating a DataFrame from the list of degree centralities in all the books
degree_evol_df = pd.DataFrame.from_records(evol).fillna(0)

# Plotting the degree centrality evolution of Eddard-Stark, Tyrion-Lannister and Jon-Snow
degree_evol_df[['Eddard-Stark', 'Tyrion-Lannister','Jon-Snow']].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f9273e7c0b8>




![png](/assets/img/game-of-thrones_files/game-of-thrones_9_1.png)

---
## 6. What's up with Stannis Baratheon?
<p>We can see that the importance of Eddard Stark dies off as the book series progresses. With Jon Snow, there is a drop in the fourth book but a sudden rise in the fifth book.</p>
<p>Now let's look at various other measures like <em>betweenness centrality</em> and <em>PageRank</em> to find important characters in our Game of Thrones character co-occurrence network and see if we can uncover some more interesting facts about this network. Let's plot the evolution of betweenness centrality of this network over the five books. We will take the evolution of the top four characters of every book and plot it.</p>


```python
# Creating a list of betweenness centrality of all the books just like we did for degree centrality
evol = [nx.betweenness_centrality(book) for book in books]

# Making a DataFrame from the list
betweenness_evol_df = pd.DataFrame.from_records(evol).fillna(0)

# Finding the top 4 characters in every book
set_of_char = set()
for i in range(5):
    set_of_char |= set(list(betweenness_evol_df.T[i].sort_values(ascending=False)[0:4].index))
list_of_char = list(set_of_char)

# Plotting the evolution of the top characters
betweenness_evol_df[list_of_char].plot(figsize=(13, 7))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f92786aeba8>




![png](/assets/img/game-of-thrones_files/game-of-thrones_11_1.png)

---
## 7. What does Google PageRank tell us about GoT?
<p>We see a peculiar rise in the importance of Stannis Baratheon over the books. In the fifth book, he is significantly more important than other characters in the network, even though he is the third most important character according to degree centrality.</p>
<p>PageRank was the initial way Google ranked web pages. It evaluates the inlinks and outlinks of webpages in the world wide web, which is, essentially, a directed network. Let's look at the importance of characters in the Game of Thrones network according to PageRank. </p>


```python
# Creating a list of pagerank of all the characters in all the books
evol = [nx.pagerank(book) for book in books]

# Making a DataFrame from the list
pagerank_evol_df = pd.DataFrame.from_records(evol).fillna(0)

# Finding the top 4 characters in every book
set_of_char = set()
for i in range(5):
    set_of_char |= set(list(pagerank_evol_df.T[i].sort_values(ascending=False)[0:4].index))
list_of_char = list(set_of_char)

# Plotting the top characters
pagerank_evol_df[list_of_char].plot(figsize=(13, 7))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f9278726780>




![png](/assets/img/game-of-thrones_files/game-of-thrones_13_1.png)

---
## 8. Correlation between different measures
<p>Stannis, Jon Snow, and Daenerys are the most important characters in the fifth book according to PageRank. Eddard Stark follows a similar curve but for degree centrality and betweenness centrality: He is important in the first book but dies into oblivion over the book series.</p>
<p>We have seen three different measures to calculate the importance of a node in a network, and all of them tells us something about the characters and their importance in the co-occurrence network. We see some names pop up in all three measures so maybe there is a strong correlation between them?</p>
<p>Let's look at the correlation between PageRank, betweenness centrality and degree centrality for the fifth book using Pearson correlation.</p>


```python
# Creating a list of pagerank, betweenness centrality, degree centrality
# of all the characters in the fifth book.
measures = [nx.pagerank(books[4]),
            nx.betweenness_centrality(books[4], weight='weight'),
            nx.degree_centrality(books[4])]

# Creating the correlation DataFrame
cor = pd.DataFrame.from_records(measures)

# Calculating the correlation
cor.T.corr().style.background_gradient()
```




<style  type="text/css" >
    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow0_col0 {
            background-color:  #023858;
            color:  #f1f1f1;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow0_col1 {
            background-color:  #fff7fb;
            color:  #000000;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow0_col2 {
            background-color:  #046299;
            color:  #f1f1f1;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow1_col0 {
            background-color:  #fff7fb;
            color:  #000000;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow1_col1 {
            background-color:  #023858;
            color:  #f1f1f1;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow1_col2 {
            background-color:  #fff7fb;
            color:  #000000;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow2_col0 {
            background-color:  #045c90;
            color:  #f1f1f1;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow2_col1 {
            background-color:  #dcdaeb;
            color:  #000000;
        }    #T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow2_col2 {
            background-color:  #023858;
            color:  #f1f1f1;
        }</style><table id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004f" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >0</th>        <th class="col_heading level0 col1" >1</th>        <th class="col_heading level0 col2" >2</th>    </tr></thead><tbody>
                <tr>
                        <th id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004flevel0_row0" class="row_heading level0 row0" >0</th>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow0_col0" class="data row0 col0" >1</td>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow0_col1" class="data row0 col1" >0.793372</td>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow0_col2" class="data row0 col2" >0.971493</td>
            </tr>
            <tr>
                        <th id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004flevel0_row1" class="row_heading level0 row1" >1</th>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow1_col0" class="data row1 col0" >0.793372</td>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow1_col1" class="data row1 col1" >1</td>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow1_col2" class="data row1 col2" >0.833816</td>
            </tr>
            <tr>
                        <th id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004flevel0_row2" class="row_heading level0 row2" >2</th>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow2_col0" class="data row2 col0" >0.971493</td>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow2_col1" class="data row2 col1" >0.833816</td>
                        <td id="T_e2e9c5fa_5a9b_11e9_9893_0242ac11004frow2_col2" class="data row2 col2" >1</td>
            </tr>
    </tbody></table>


---
## 9. Conclusion
<p>We see a high correlation between these three measures for our character co-occurrence network.</p>
<p>So we've been looking at different ways to find the important characters in the Game of Thrones co-occurrence network. According to degree centrality, Eddard Stark is the most important character initially in the books. But who is/are the most important character(s) in the fifth book according to these three measures? </p>


```python
# Finding the most important character in the fifth book,  
# according to degree centrality, betweenness centrality and pagerank.
#p_rank, b_cent, d_cent = cor[2].idmax()

# Printing out the top character accoding to the three measures
cor.idxmax(axis=1)
```




    0             Jon-Snow
    1    Stannis-Baratheon
    2             Jon-Snow
    dtype: object

---
## Further Reading

If you're more interested in network analysis you can have a look at the following resources:

- [networkofthrones](https://networkofthrones.wordpress.com/) is a blog dedicated to data collection and all things networks about Game of Thrones.
- [bookworm](https://github.com/harrisonpim/bookworm): Extracting social networks from novels.
---
## Dig deeper?

You can find out more about this project at [Github](https://github.com/Kau5h1K/network-of-thrones).
{: .notice}
