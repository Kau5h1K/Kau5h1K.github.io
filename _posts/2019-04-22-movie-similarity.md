---
layout: post
title: "Finding Movie Similarity from Plot Summaries"
tags: [ Project, python, pandas, Cleaning Data, hierarchy, clustering, dendrogram, cosine-similarity, Natural Language Processing, NLP, Tokenize, Stem, SnowballStemmer, KMeans, TfidfVectorizer, regex, topical similarity]
date: 2019-04-22
excerpt: "Natural Language Processing (NLP) is an exciting field of study for data scientists where they develop algorithms that can make sense out of conversational language used by humans. <br/>
In this Project, I have used NLP to find the degree of similarity between movies based on their plots available on IMDb and Wikipedia."
comments: True
project: True
---

---
## Data
This project uses a dataset which is available [here](https://github.com/Kau5h1K/movie-similarity/tree/master/datasets).The dataset contains the titles of the top 100 movies on [IMDb](https://www.imdb.com/) as well as each movie's plot summary from both IMDb and Wikipedia.

---
## 1. Import and observe dataset
<p>We all love watching movies! There are some movies we like, some we don't. Most people have a preference for movies of a similar genre. Some of us love watching action movies, while some of us like watching horror. Some of us like watching movies that have ninjas in them, while some of us like watching superheroes.</p>
<p>Movies within a genre often share common base parameters. Consider the following two movies:</p>
<p><img style="margin:5px 20px 5px 1px; height: 250px; display: inline-block;" alt="2001: A Space Odyssey" src="https://assets.datacamp.com/production/project_648/img/movie1.jpg">
<img style="margin:5px 20px 5px 1px; height: 250px; display: inline-block;" alt="Close Encounters of the Third Kind" src="https://assets.datacamp.com/production/project_648/img/movie2.jpg"></p>
<p>Both movies, <em>2001: A Space Odyssey</em> and <em>Close Encounters of the Third Kind</em>, are movies based on aliens coming to Earth. I've seen both, and they indeed share many similarities. We could conclude that both of these fall into the same genre of movies based on intuition, but that's no fun in a data science context. In this notebook, we will quantify the similarity of movies based on their plot summaries available on IMDb and Wikipedia, then separate them into groups, also known as clusters. We'll create a dendrogram to represent how closely the movies are related to each other.</p>
<p>Let's start by importing the dataset and observing the data provided.</p>


```python
# Import modules
import numpy as np
import pandas as pd
import nltk

# Set seed for reproducibility
np.random.seed(5)

# Read in IMDb and Wikipedia movie data (both in same file)
movies_df = pd.read_csv('datasets/movies.csv')

print("Number of movies loaded: %s " % (len(movies_df)))

# Display the data
movies_df
```

    Number of movies loaded: 100





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
      <th>rank</th>
      <th>title</th>
      <th>genre</th>
      <th>wiki_plot</th>
      <th>imdb_plot</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>The Godfather</td>
      <td>[u' Crime', u' Drama']</td>
      <td>On the day of his only daughter's wedding, Vit...</td>
      <td>In late summer 1945, guests are gathered for t...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>The Shawshank Redemption</td>
      <td>[u' Crime', u' Drama']</td>
      <td>In 1947, banker Andy Dufresne is convicted of ...</td>
      <td>In 1947, Andy Dufresne (Tim Robbins), a banker...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Schindler's List</td>
      <td>[u' Biography', u' Drama', u' History']</td>
      <td>In 1939, the Germans move Polish Jews into the...</td>
      <td>The relocation of Polish Jews from surrounding...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Raging Bull</td>
      <td>[u' Biography', u' Drama', u' Sport']</td>
      <td>In a brief scene in 1964, an aging, overweight...</td>
      <td>The film opens in 1964, where an older and fat...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Casablanca</td>
      <td>[u' Drama', u' Romance', u' War']</td>
      <td>It is early December 1941. American expatriate...</td>
      <td>In the early years of World War II, December 1...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>One Flew Over the Cuckoo's Nest</td>
      <td>[u' Drama']</td>
      <td>In 1963 Oregon, Randle Patrick "Mac" McMurphy ...</td>
      <td>In 1963 Oregon, Randle Patrick McMurphy (Nicho...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Gone with the Wind</td>
      <td>[u' Drama', u' Romance', u' War']</td>
      <td>\nPart 1\n  \n  Part 1  Part 1  \n  \n  On the...</td>
      <td>The film opens in Tara, a cotton plantation ow...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>Citizen Kane</td>
      <td>[u' Drama', u' Mystery']</td>
      <td>\n\n\n\nOrson Welles as Charles Foster Kane\n\...</td>
      <td>It's 1941, and newspaper tycoon Charles Foster...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>The Wizard of Oz</td>
      <td>[u' Adventure', u' Family', u' Fantasy', u' Mu...</td>
      <td>The film starts in sepia-tinted Kansas in the ...</td>
      <td>Dorothy Gale (Judy Garland) is an orphaned tee...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>Titanic</td>
      <td>[u' Drama', u' Romance']</td>
      <td>In 1996, treasure hunter Brock Lovett and his ...</td>
      <td>In 1996, treasure hunter Brock Lovett and his ...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>Lawrence of Arabia</td>
      <td>[u' Adventure', u' Biography', u' Drama', u' H...</td>
      <td>]  \n  The film is presented in two parts, s...</td>
      <td>In 1935, T. E. Lawrence (Peter O'Toole) is kil...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>The Godfather: Part II</td>
      <td>[u' Crime', u' Drama']</td>
      <td>\nIn 1901 Corleone, Sicily, nine-year-old Vito...</td>
      <td>The Godfather Part II presents two parallel st...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>Psycho</td>
      <td>[u' Horror', u' Mystery', u' Thriller']</td>
      <td>Patrick Bateman is a wealthy investment banker...</td>
      <td>In a Phoenix hotel room on a Friday afternoon,...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>Sunset Blvd.</td>
      <td>[u' Drama', u' Film-Noir']</td>
      <td>At a Sunset Boulevard mansion, the body of Joe...</td>
      <td>The film opens with the camera tracking down S...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>Vertigo</td>
      <td>[u' Mystery', u' Romance', u' Thriller']</td>
      <td>ridge, Fort Point\n\n  \n  \n\n\n"Madeleine" a...</td>
      <td>A woman's face gives way to a kaleidoscope of ...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>On the Waterfront</td>
      <td>[u' Crime', u' Drama']</td>
      <td>Mob-connected union boss Johnny Friendly (Lee ...</td>
      <td>Terry Malloy (Marlon Brando) once dreamt of be...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>Forrest Gump</td>
      <td>[u' Drama', u' Romance']</td>
      <td>While waiting at a bus stop in 1981, Forrest G...</td>
      <td>The film begins with a feather falling to the ...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>The Sound of Music</td>
      <td>[u' Biography', u' Drama', u' Family', u' Musi...</td>
      <td>In 1938, while living as a young postulant at ...</td>
      <td>The widowed, retired Austrian naval officer, C...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>West Side Story</td>
      <td>[u' Crime', u' Drama', u' Musical', u' Romance...</td>
      <td>]  ]  \n  In the West Side's Lincoln Square ne...</td>
      <td>A fight set to music between an American gang,...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>Star Wars</td>
      <td>[u' Action', u' Adventure', u' Fantasy', u' Sc...</td>
      <td>The galaxy is in a civil war, and spies for th...</td>
      <td>\nNote: Italicized paragraphs denote scenes ad...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20</td>
      <td>E.T. the Extra-Terrestrial</td>
      <td>[u' Adventure', u' Family', u' Sci-Fi']</td>
      <td>In a California forest, a group of alien botan...</td>
      <td>In a forested area overlooking a sprawling sub...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21</td>
      <td>2001: A Space Odyssey</td>
      <td>[u' Mystery', u' Sci-Fi']</td>
      <td>The film consists of four major sections, all ...</td>
      <td>To Richard Strauss' tone poem "Thus Spake Zara...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22</td>
      <td>The Silence of the Lambs</td>
      <td>[u' Crime', u' Drama', u' Thriller']</td>
      <td>is pulled from her training at the FBI Academy...</td>
      <td>Promising FBI Academy student Clarice Starling...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23</td>
      <td>Chinatown</td>
      <td>[u' Drama', u' Mystery', u' Thriller']</td>
      <td>A woman identifying herself as Evelyn Mulwray ...</td>
      <td>Set in 1937 Los Angeles, a private investigato...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24</td>
      <td>The Bridge on the River Kwai</td>
      <td>[u' Adventure', u' Drama', u' War']</td>
      <td>In World War II, British prisoners arrive at a...</td>
      <td>this synopsis is primarily from the wikipedia ...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25</td>
      <td>Singin' in the Rain</td>
      <td>[u' Comedy', u' Musical', u' Romance']</td>
      <td>Don Lockwood is a popular silent film star wit...</td>
      <td>Don Lockwood (Gene Kelly) is a popular silent ...</td>
    </tr>
    <tr>
      <th>26</th>
      <td>26</td>
      <td>It's a Wonderful Life</td>
      <td>[u' Drama', u' Family', u' Fantasy']</td>
      <td>\n\n\n\nDonna Reed (as Mary Bailey) and James ...</td>
      <td>This movie is about a divine intervention by a...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>27</td>
      <td>Some Like It Hot</td>
      <td>[u' Comedy']</td>
      <td>It is February 1929 in the city of Chicago. Jo...</td>
      <td>Joe and Jerry, a saxophonist and bassist, resp...</td>
    </tr>
    <tr>
      <th>28</th>
      <td>28</td>
      <td>12 Angry Men</td>
      <td>[u' Drama']</td>
      <td>The story begins in a New York City courthous...</td>
      <td>A teenaged Hispanic boy has just been tried fo...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>29</td>
      <td>Dr. Strangelove or: How I Learned to Stop Worr...</td>
      <td>[u' Comedy', u' War']</td>
      <td>United States Air Force Brigadier General Jack...</td>
      <td>At the Burpelson U.S. Air Force Base somewhere...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>70</td>
      <td>Rain Man</td>
      <td>[u' Drama']</td>
      <td>Charlie Babbitt is in the middle of importing ...</td>
      <td>Charlie Babbitt (Tom Cruise), a Los Angeles ca...</td>
    </tr>
    <tr>
      <th>71</th>
      <td>71</td>
      <td>Annie Hall</td>
      <td>[u' Comedy', u' Drama', u' Romance']</td>
      <td>The comedian Alvy Singer (Woody Allen) is tryi...</td>
      <td>Annie Hall is a film about a comedian, Alvy Si...</td>
    </tr>
    <tr>
      <th>72</th>
      <td>72</td>
      <td>Out of Africa</td>
      <td>[u' Biography', u' Drama', u' Romance']</td>
      <td>The story begins in 1913 in Denmark, when Kare...</td>
      <td>[Out Of Africa]A well-heeled Danish lady goes ...</td>
    </tr>
    <tr>
      <th>73</th>
      <td>73</td>
      <td>Good Will Hunting</td>
      <td>[u' Drama']</td>
      <td>Twenty-year-old Will Hunting (Damon) of South ...</td>
      <td>Though Will Hunting (Matt Damon) has genius-le...</td>
    </tr>
    <tr>
      <th>74</th>
      <td>74</td>
      <td>Terms of Endearment</td>
      <td>[u' Comedy', u' Drama']</td>
      <td>Aurora Greenway (Shirley MacLaine) and her dau...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>75</th>
      <td>75</td>
      <td>Tootsie</td>
      <td>[u' Comedy', u' Drama', u' Romance']</td>
      <td>Michael Dorsey (Dustin Hoffman) is a respected...</td>
      <td>Michael Dorsey is an actor living and working ...</td>
    </tr>
    <tr>
      <th>76</th>
      <td>76</td>
      <td>Fargo</td>
      <td>[u' Crime', u' Drama', u' Thriller']</td>
      <td>In the winter of 1987, Minneapolis car salesma...</td>
      <td>The movie opens with a car towing a new tan Ol...</td>
    </tr>
    <tr>
      <th>77</th>
      <td>77</td>
      <td>Giant</td>
      <td>[u' Drama', u' Romance']</td>
      <td>Jordan "Bick" Benedict (Rock Hudson), head of ...</td>
      <td>In the early 1920s, Jordan "Bick" Benedict (Ro...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>78</td>
      <td>The Grapes of Wrath</td>
      <td>[u' Drama']</td>
      <td>The film opens with Tom Joad (Henry Fonda), re...</td>
      <td>After serving four years in prison for killing...</td>
    </tr>
    <tr>
      <th>79</th>
      <td>79</td>
      <td>Shane</td>
      <td>[u' Drama', u' Romance', u' Western']</td>
      <td>\n\n\n\nAlan Ladd and Jean Arthur\n\n  \n  \n\...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>80</th>
      <td>80</td>
      <td>The Green Mile</td>
      <td>[u' Crime', u' Drama', u' Fantasy', u' Mystery']</td>
      <td>In a Louisiana nursing home in 1999, Paul Edge...</td>
      <td>The movie begins with an old man named Paul Ed...</td>
    </tr>
    <tr>
      <th>81</th>
      <td>81</td>
      <td>Close Encounters of the Third Kind</td>
      <td>[u' Drama', u' Sci-Fi']</td>
      <td>In the Sonoran Desert, French scientist Claude...</td>
      <td>In what appers to be the Sonoran Desert; in or...</td>
    </tr>
    <tr>
      <th>82</th>
      <td>82</td>
      <td>Network</td>
      <td>[u' Drama']</td>
      <td>Howard Beale, the longtime anchor of the Union...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>83</th>
      <td>83</td>
      <td>Nashville</td>
      <td>[u' Drama', u' Music']</td>
      <td>The overarching plot takes place over five day...</td>
      <td>The overarching plot takes place over five day...</td>
    </tr>
    <tr>
      <th>84</th>
      <td>84</td>
      <td>The Graduate</td>
      <td>[u' Comedy', u' Drama', u' Romance']</td>
      <td>Benjamin Braddock, going on from twenty to twe...</td>
      <td>The film explores the life of 21-year-old Ben ...</td>
    </tr>
    <tr>
      <th>85</th>
      <td>85</td>
      <td>American Graffiti</td>
      <td>[u' Comedy', u' Drama']</td>
      <td>In late August 1962 recent high school graduat...</td>
      <td>It's the last night of the summer in 1962, and...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>86</td>
      <td>Pulp Fiction</td>
      <td>[u' Crime', u' Drama', u' Thriller']</td>
      <td>The Diner"  "Prologue—The Diner"  \n  "Pumpkin...</td>
      <td>Late one morning in the Hawthorne Grill, a res...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>87</td>
      <td>The African Queen</td>
      <td>[u' Adventure', u' Romance', u' War']</td>
      <td>Robert Morley and Katharine Hepburn play Samue...</td>
      <td>An English spinster, Rose (Katharine Hepburn),...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>88</td>
      <td>Stagecoach</td>
      <td>[u' Adventure', u' Western']</td>
      <td>In 1880, a motley group of strangers boards th...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89</th>
      <td>89</td>
      <td>Mutiny on the Bounty</td>
      <td>[u' Adventure', u' Drama', u' History']</td>
      <td>In the year 1787, the Bounty sets sail from En...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>90</th>
      <td>90</td>
      <td>The Maltese Falcon</td>
      <td>[u' Drama', u' Film-Noir', u' Mystery']</td>
      <td>\n\n\nIn 1539 the Knight Templars of Malta, pa...</td>
      <td>Private eye Sam Spade and his partner Miles Ar...</td>
    </tr>
    <tr>
      <th>91</th>
      <td>91</td>
      <td>A Clockwork Orange</td>
      <td>[u' Crime', u' Drama', u' Sci-Fi']</td>
      <td>In futuristic London, Alex DeLarge is the lead...</td>
      <td>A bit of the old ultra-violence".London, Engla...</td>
    </tr>
    <tr>
      <th>92</th>
      <td>92</td>
      <td>Taxi Driver</td>
      <td>[u' Crime', u' Drama']</td>
      <td>Travis Bickle, an honorably discharged U.S. Ma...</td>
      <td>Travis Bickle (Robert De Niro) goes to a New Y...</td>
    </tr>
    <tr>
      <th>93</th>
      <td>93</td>
      <td>Wuthering Heights</td>
      <td>[u' Drama', u' Romance']</td>
      <td>A traveller named Lockwood (Miles Mander) is c...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>94</th>
      <td>94</td>
      <td>Double Indemnity</td>
      <td>[u' Crime', u' Drama', u' Film-Noir', u' Thril...</td>
      <td>\n\n\n\nNeff confesses into a Dictaphone.\n\n ...</td>
      <td>Walter Neff (MacMurray) is a successful insura...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>95</td>
      <td>Rebel Without a Cause</td>
      <td>[u' Drama']</td>
      <td>\n\n\n\nJim Stark is in police custody.\n\n  \...</td>
      <td>Shortly after moving to Los Angeles with his p...</td>
    </tr>
    <tr>
      <th>96</th>
      <td>96</td>
      <td>Rear Window</td>
      <td>[u' Mystery', u' Thriller']</td>
      <td>\n\n\n\nJames Stewart as L.B. Jefferies\n\n  \...</td>
      <td>L.B. "Jeff" Jeffries (James Stewart) recuperat...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>97</td>
      <td>The Third Man</td>
      <td>[u' Film-Noir', u' Mystery', u' Thriller']</td>
      <td>\n\n\n\nSocial network mapping all major chara...</td>
      <td>Sights of Vienna, Austria, flash across the sc...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>98</td>
      <td>North by Northwest</td>
      <td>[u' Mystery', u' Thriller']</td>
      <td>Advertising executive Roger O. Thornhill is mi...</td>
      <td>At the end of an ordinary work day, advertisin...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>99</td>
      <td>Yankee Doodle Dandy</td>
      <td>[u' Biography', u' Drama', u' Musical']</td>
      <td>\n  In the early days of World War II, Cohan ...</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 5 columns</p>
</div>


---
## 2. Combine Wikipedia and IMDb plot summaries
<p>The dataset we imported currently contains two columns titled <code>wiki_plot</code> and <code>imdb_plot</code>. They are the plot found for the movies on Wikipedia and IMDb, respectively. The text in the two columns is similar, however, they are often written in different tones and thus provide context on a movie in a different manner of linguistic expression. Further, sometimes the text in one column may mention a feature of the plot that is not present in the other column. For example, consider the following plot extracts from <em>The Godfather</em>:</p>
<ul>
<li>Wikipedia: "On the day of his only daughter's wedding, Vito Corleone"</li>
<li>IMDb: "In late summer 1945, guests are gathered for the wedding reception of Don Vito Corleone's daughter Connie"</li>
</ul>
<p>While the Wikipedia plot only mentions it is the day of the daughter's wedding, the IMDb plot also mentions the year of the scene and the name of the daughter. </p>
<p>Let's combine both the columns to avoid the overheads in computation associated with extra columns to process.</p>


```python
# Combine wiki_plot and imdb_plot into a single column
movies_df['plot'] = movies_df['wiki_plot'].astype(str) + "\n" + \
                 movies_df['imdb_plot'].astype(str)

# Inspect the new DataFrame
movies_df.head()
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
      <th>rank</th>
      <th>title</th>
      <th>genre</th>
      <th>wiki_plot</th>
      <th>imdb_plot</th>
      <th>plot</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>The Godfather</td>
      <td>[u' Crime', u' Drama']</td>
      <td>On the day of his only daughter's wedding, Vit...</td>
      <td>In late summer 1945, guests are gathered for t...</td>
      <td>On the day of his only daughter's wedding, Vit...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>The Shawshank Redemption</td>
      <td>[u' Crime', u' Drama']</td>
      <td>In 1947, banker Andy Dufresne is convicted of ...</td>
      <td>In 1947, Andy Dufresne (Tim Robbins), a banker...</td>
      <td>In 1947, banker Andy Dufresne is convicted of ...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Schindler's List</td>
      <td>[u' Biography', u' Drama', u' History']</td>
      <td>In 1939, the Germans move Polish Jews into the...</td>
      <td>The relocation of Polish Jews from surrounding...</td>
      <td>In 1939, the Germans move Polish Jews into the...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Raging Bull</td>
      <td>[u' Biography', u' Drama', u' Sport']</td>
      <td>In a brief scene in 1964, an aging, overweight...</td>
      <td>The film opens in 1964, where an older and fat...</td>
      <td>In a brief scene in 1964, an aging, overweight...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Casablanca</td>
      <td>[u' Drama', u' Romance', u' War']</td>
      <td>It is early December 1941. American expatriate...</td>
      <td>In the early years of World War II, December 1...</td>
      <td>It is early December 1941. American expatriate...</td>
    </tr>
  </tbody>
</table>
</div>


---
## 3. Tokenization
<p>Tokenization is the process  by which we break down articles into individual sentences or words, as needed. Besides the tokenization method provided by NLTK, we might have to perform additional filtration to remove tokens which are entirely numeric values or punctuation.</p>
<p>While a program may fail to build context from "While waiting at a bus stop in 1981" (<em>Forrest Gump</em>), because this string would not match in any dictionary, it is possible to build context from the words "while", "waiting" or "bus" because they are present in the English dictionary. </p>
<p>Let us perform tokenization on a small extract from <em>The Godfather</em>.</p>


```python
# Tokenize a paragraph into sentences and store in sent_tokenized
sent_tokenized = [sent for sent in nltk.sent_tokenize("""
                        Today (May 19, 2016) is his only daughter's wedding.
                        Vito Corleone is the Godfather.""")]

# Word Tokenize first sentence from sent_tokenized, save as words_tokenized
words_tokenized = [word for word in nltk.word_tokenize(sent_tokenized[0])]

# Remove tokens that do not contain any letters from words_tokenized
import re

filtered = [word for word in words_tokenized if re.search('[a-zA-Z]+', word)]

# Display filtered words to observe words after tokenization
filtered
```




    ['Today', 'May', 'is', 'his', 'only', 'daughter', "'s", 'wedding']


---
## 4. Stemming
<p>Stemming is the process by which we bring down a word from its different forms to the root word. This helps us establish meaning to different forms of the same words without having to deal with each form separately. For example, the words 'fishing', 'fished', and 'fisher' all get stemmed to the word 'fish'.</p>
<p>Consider the following sentences:</p>
<ul>
<li>"Young William Wallace witnesses the treachery of Longshanks" ~ <em>Gladiator</em></li>
<li>"escapes to the city walls only to witness Cicero's death" ~ <em>Braveheart</em></li>
</ul>
<p>Instead of building separate dictionary entries for both witnesses and witness, which mean the same thing outside of quantity, stemming them reduces them to 'wit'.</p>
<p>There are different algorithms available for stemming such as the Porter Stemmer, Snowball Stemmer, etc. We shall use the Snowball Stemmer.</p>


```python
# Import the SnowballStemmer to perform stemming
from nltk.stem.snowball import SnowballStemmer
# Create an English language SnowballStemmer object
stemmer = SnowballStemmer("english")

# Print filtered to observe words without stemming
print("Without stemming: ", filtered)

# Stem the words from filtered and store in stemmed_words
stemmed_words = [stemmer.stem(word) for word in filtered]

# Print the stemmed_words to observe words after stemming
print("After stemming:   ", stemmed_words)
```

    Without stemming:  ['Today', 'May', 'is', 'his', 'only', 'daughter', "'s", 'wedding']
    After stemming:    ['today', 'may', 'is', 'his', 'onli', 'daughter', "'s", 'wed']

---
## 5. Club together Tokenize & Stem
<p>We are now able to tokenize and stem sentences. But we may have to use the two functions repeatedly one after the other to handle a large amount of data, hence we can think of wrapping them in a function and passing the text to be tokenized and stemmed as the function argument. Then we can pass the new wrapping function, which shall perform both tokenizing and stemming instead of just tokenizing, as the tokenizer argument while creating the TF-IDF vector of the text.  </p>
<p>What difference does it make though? Consider the sentence from the plot of <em>The Godfather</em>: "Today (May 19, 2016) is his only daughter's wedding." If we do a 'tokenize-only' for this sentence, we have the following result:</p>
<blockquote>
  <p>'today', 'may', 'is', 'his', 'only', 'daughter', "'s", 'wedding'</p>
</blockquote>
<p>But when we do a 'tokenize-and-stem' operation we get:</p>
<blockquote>
  <p>'today', 'may', 'is', 'his', 'onli', 'daughter', "'s", 'wed'</p>
</blockquote>
<p>All the words are in their root form, which will lead to a better establishment of meaning as some of the non-root forms may not be present in the NLTK training corpus.</p>


```python
# Define a function to perform both stemming and tokenization
def tokenize_and_stem(text):

    # Tokenize by sentence, then by word
    tokens = [word for sent in nltk.sent_tokenize(text) for word in nltk.word_tokenize(sent)]

    # Filter out raw tokens to remove noise
    filtered_tokens = [token for token in tokens if re.search('[a-zA-Z]', token)]

    # Stem the filtered tokens
    stems = [stemmer.stem(token) for token in filtered_tokens]

    return stems

words_stemmed = tokenize_and_stem("Today (May 19, 2016) is his only daughter's wedding.")
print(words_stemmed)
```

    ['today', 'may', 'is', 'his', 'onli', 'daughter', "'s", 'wed']

---
## 6. Create TfidfVectorizer
<p>Computers do not <em>understand</em> text. These are machines only capable of understanding numbers and performing numerical computation. Hence, we must convert our textual plot summaries to numbers for the computer to be able to extract meaning from them. One simple method of doing this would be to count all the occurrences of each word in the entire vocabulary and return the counts in a vector. Enter <code>CountVectorizer</code>.</p>
<p>Consider the word 'the'. It appears quite frequently in almost all movie plots and will have a high count in each case. But obviously, it isn't the theme of all the movies! <a href="https://campus.datacamp.com/courses/natural-language-processing-fundamentals-in-python/simple-topic-identification?ex=11">Term Frequency-Inverse Document Frequency</a> (TF-IDF) is one method which overcomes the shortcomings of <code>CountVectorizer</code>. The Term Frequency of a word is the measure of how often it appears in a document, while the Inverse Document Frequency is the parameter which reduces the importance of a word if it frequently appears in several documents.</p>
<p>For example, when we apply the TF-IDF on the first 3 sentences from the plot of <em>The Wizard of Oz</em>, we are told that the most important word there is 'Toto', the pet dog of the lead character. This is because the movie begins with 'Toto' biting someone due to which the journey of Oz begins!</p>
<p>In simplest terms, TF-IDF recognizes words which are unique and important to any given document. Let's create one for our purposes.</p>


```python
# Import TfidfVectorizer to create TF-IDF vectors
from sklearn.feature_extraction.text import TfidfVectorizer
# Instantiate TfidfVectorizer object with stopwords and tokenizer
# parameters for efficient processing of text
tfidf_vectorizer = TfidfVectorizer(max_df=0.8, max_features=200000,
                                 min_df=0.2, stop_words='english',
                                 use_idf=True, tokenizer=tokenize_and_stem,
                                 ngram_range=(1,3))
```

---
## 7. Fit transform TfidfVectorizer
<p>Once we create a TF-IDF Vectorizer, we must fit the text to it and then transform the text to produce the corresponding numeric form of the data which the computer will be able to understand and derive meaning from. To do this, we use the <code>fit_transform()</code> method of the <code>TfidfVectorizer</code> object. </p>
<p>If we observe the <code>TfidfVectorizer</code> object we created, we come across a parameter stopwords. 'stopwords' are those words in a given text which do not contribute considerably towards the meaning of the sentence and are generally grammatical filler words. For example, in the sentence 'Dorothy Gale lives with her dog Toto on the farm of her Aunt Em and Uncle Henry', we could drop the words 'her' and 'the', and still have a similar overall meaning to the sentence. Thus, 'her' and 'the' are stopwords and can be conveniently dropped from the sentence. </p>
<p>On setting the stopwords to 'english', we direct the vectorizer to drop all stopwords from a pre-defined list of English language stopwords present in the nltk module. Another parameter, <code>ngram_range</code>, defines the length of the ngrams to be formed while vectorizing the text.</p>


```python
# Fit and transform the tfidf_vectorizer with the "plot" of each movie
# to create a vector representation of the plot summaries
tfidf_matrix = tfidf_vectorizer.fit_transform([x for x in movies_df["plot"]])

print(tfidf_matrix.shape)
```

    (100, 564)

---
## 8. Import KMeans and create clusters
<p>To determine how closely one movie is related to the other by the help of unsupervised learning, we can use clustering techniques. Clustering is the method of grouping together a number of items such that they exhibit similar properties. According to the measure of similarity desired, a given sample of items can have one or more clusters. </p>
<p>A good basis of clustering in our dataset could be the genre of the movies. Say we could have a cluster '0' which holds movies of the 'Drama' genre. We would expect movies like <em>Chinatown</em> or <em>Psycho</em> to belong to this cluster. Similarly, the cluster '1' in this project holds movies which belong to the 'Adventure' genre (<em>Lawrence of Arabia</em> and the <em>Raiders of the Lost Ark</em>, for example).</p>
<p>K-means is an algorithm which helps us to implement clustering in Python. The name derives from its method of implementation: the given sample is divided into <b><i>K</i></b> clusters where each cluster is denoted by the <b><i>mean</i></b> of all the items lying in that cluster. </p>
<p>We get the following distribution for the clusters:</p>
<p><img src="https://assets.datacamp.com/production/project_648/img/bar_clusters.png" alt="bar graph of clusters"></p>


```python
# Import k-means to perform clusters
from sklearn.cluster import KMeans

km = KMeans(n_clusters=5)

# Fit the k-means object with tfidf_matrix
km.fit(tfidf_matrix )

clusters = km.labels_.tolist()

# Create a column cluster to denote the generated cluster for each movie
movies_df["cluster"] = clusters

# Display number of films per cluster (clusters from 0 to 4)
movies_df['cluster'].value_counts()
```




    2    35
    1    21
    3    20
    0    17
    4     7
    Name: cluster, dtype: int64


---
## 9. Calculate similarity distance
<p>Consider the following two sentences from the movie <em>The Wizard of Oz</em>: </p>
<blockquote>
  <p>"they find in the Emerald City"</p>
  <p>"they finally reach the Emerald City"</p>
</blockquote>
<p>If we put the above sentences in a <code>CountVectorizer</code>, the vocabulary produced would be "they, find, in, the, Emerald, City, finally, reach" and the vectors for each sentence would be as follows: </p>
<blockquote>
  <p>1, 1, 1, 1, 1, 1, 0, 0</p>
  <p>1, 0, 0, 1, 1, 1, 1, 1</p>
</blockquote>
<p>When we calculate the cosine angle formed between the vectors represented by the above, we get a score of 0.667. This means the above sentences are very closely related. <em>Similarity distance</em> is 1 - <a href="https://en.wikipedia.org/wiki/Cosine_similarity">cosine similarity angle</a>. This follows from that if the vectors are similar, the cosine of their angle would be 1 and hence, the distance between then would be 1 - 1 = 0.</p>
<p>Let's calculate the similarity distance for all of our movies.</p>


```python
# Import cosine_similarity to calculate similarity of movie plots
from sklearn.metrics.pairwise import cosine_similarity

# Calculate the similarity distance
similarity_distance = 1 - cosine_similarity(tfidf_matrix)
cosine_similarity(tfidf_matrix)
```




    array([[1.        , 0.19935288, 0.23705907, ..., 0.35978041, 0.32924425,
            0.17597887],
           [0.19935288, 1.        , 0.26652215, ..., 0.26645081, 0.22839076,
            0.1070252 ],
           [0.23705907, 0.26652215, 1.        , ..., 0.27574688, 0.25051029,
            0.14132677],
           ...,
           [0.35978041, 0.26645081, 0.27574688, ..., 1.        , 0.54823341,
            0.08240191],
           [0.32924425, 0.22839076, 0.25051029, ..., 0.54823341, 1.        ,
            0.13140014],
           [0.17597887, 0.1070252 , 0.14132677, ..., 0.08240191, 0.13140014,
            1.        ]])


---
## 10. Import Matplotlib, Linkage, and Dendrograms
<p>We shall now create a tree-like diagram (called a dendrogram) of the movie titles to help us understand the level of similarity between them visually. Dendrograms help visualize the results of hierarchical clustering, which is an alternative to k-means clustering. Two pairs of movies at the same level of hierarchical clustering are expected to have similar strength of similarity between the corresponding pairs of movies. For example, the movie <em>Fargo</em> would be as similar to <em>North By Northwest</em> as the movie <em>Platoon</em> is to <em>Saving Private Ryan</em>, given both the pairs exhibit the same level of the hierarchy.</p>
<p>Let's import the modules we'll need to create our dendrogram.</p>


```python
# Import matplotlib.pyplot for plotting graphs
import matplotlib.pyplot as plt
# Configure matplotlib to display the output inline
%matplotlib inline

# Import modules necessary to plot dendrogram
from scipy.cluster.hierarchy import linkage, dendrogram
```

---
## 11. Create merging and plot dendrogram
<p>We shall plot a dendrogram of the movies whose similarity measure will be given by the similarity distance we previously calculated. The lower the similarity distance between any two movies, the lower their linkage will make an intercept on the y-axis. For instance, the lowest dendrogram linkage we shall discover will be between the movies, <em>It's a Wonderful Life</em> and <em>A Place in the Sun</em>. This indicates that the movies are very similar to each other in their plots.</p>


```python
# Create mergings matrix
mergings = linkage(similarity_distance, method='complete')

# Plot the dendrogram, using title as label column
dendrogram_ = dendrogram(mergings,
               labels=[x for x in movies_df["title"]],
               leaf_rotation=90,
               leaf_font_size=16,
)

# Adjust the plot
fig = plt.gcf()
_ = [lbl.set_color('r') for lbl in plt.gca().get_xmajorticklabels()]
fig.set_size_inches(108, 21)

# Show the plotted dendrogram
plt.show()
```


![png](/assets/img/movie-similarity_files/movie-similarity_21_0.png)

---
## 12. Which movies are most similar?
<p>We can now determine the similarity between movies based on their plots! To wrap up, let's answer one final question: which movie is most similar to the movie <em>Braveheart</em>?</p>


```python
# Answer the question
ans = "Gladiator"
print(ans)
```

    Gladiator

---
## Dig deeper?

You can find out more about this project at [Github](https://github.com/Kau5h1K/movie-similarity).
{: .notice}
