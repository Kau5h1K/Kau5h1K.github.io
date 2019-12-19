---
layout: post
title: "Super Bowl Analysis"
tags: [ Project, python, pandas, Data visualization, seaborn]
date: 2019-03-20
excerpt: "Whether or not you like football, the Super Bowl is a spectacle. There's drama in the form of blowouts, comebacks, and controversy in the games themselves. There are the ridiculously expensive ads, some hilarious, others gut-wrenching, thought-provoking, and weird. The half-time shows with the biggest musicians in the world, sometimes riding giant mechanical tigers or leaping from the roof of the stadium. And in this Project, I intend to find out how some of the elements of this show interact with each other. "
comments: True
project: True
badge: Passion
badge-color: "purple"
---

---
## Data
This project uses a dataset which is available [here](https://github.com/Kau5h1K/superbowl-analysis/tree/master/datasets). The dataset used in this Project was scraped and polished from Wikipedia. It is made up of three CSV files, one with [game data](https://en.wikipedia.org/wiki/List_of_Super_Bowl_champions), one with TV [data](https://en.wikipedia.org/wiki/Super_Bowl_television_ratings), and one with halftime musician data for all 52 Super Bowls through 2018.

---
## 1. TV, halftime shows, and the Big Game
<p>Whether or not you like football, the Super Bowl is a spectacle. There's a little something for everyone at your Super Bowl party. Drama in the form of blowouts, comebacks, and controversy for the sports fan. There are the ridiculously expensive ads, some hilarious, others gut-wrenching, thought-provoking, and weird. The half-time shows with the biggest musicians in the world, sometimes <a href="https://youtu.be/ZD1QrIe--_Y?t=14">riding giant mechanical tigers</a> or <a href="https://youtu.be/mjrdywp5nyE?t=62">leaping from the roof of the stadium</a>. It's a show, baby. And in this notebook, we're going to find out how some of the elements of this show interact with each other. After exploring and cleaning our data a little, I'm going to answer questions like:</p>
<ul>
<li>What are the most extreme game outcomes?</li>
<li>How does the game affect television viewership?</li>
<li>How have viewership, TV ratings, and ad cost evolved over time?</li>
<li>Who are the most prolific musicians in terms of halftime show performances?</li>
</ul>
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_684/img/left_shark.jpg" alt="Left Shark Steals The Show">
<em><a href="https://www.flickr.com/photos/huntleypaton/16464994135/in/photostream/">Left Shark Steals The Show</a>. Katy Perry performing at halftime of Super Bowl XLIX. Photo by Huntley Paton. Attribution-ShareAlike 2.0 Generic (CC BY-SA 2.0).</em></p>
<p>The dataset I'll use was <a href="https://en.wikipedia.org/wiki/Web_scraping">scraped</a> and polished from Wikipedia. It is made up of three CSV files, one with <a href="https://en.wikipedia.org/wiki/List_of_Super_Bowl_champions">game data</a>, one with <a href="https://en.wikipedia.org/wiki/Super_Bowl_television_ratings">TV data</a>, and one with <a href="https://en.wikipedia.org/wiki/List_of_Super_Bowl_halftime_shows">halftime musician data</a> for all 52 Super Bowls through 2018. Let's take a look, using <code>display()</code> instead of <code>print()</code> since its output is much prettier in Jupyter Notebooks.</p>


```python
# Import pandas
import pandas as pd

# Load the CSV data into DataFrames
super_bowls = pd.read_csv('datasets/super_bowls.csv')
tv = pd.read_csv('datasets/tv.csv')
halftime_musicians = pd.read_csv('datasets/halftime_musicians.csv')

# Display the first five rows of each DataFrame
display(super_bowls)
display(tv)
display(halftime_musicians)

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
      <th>date</th>
      <th>super_bowl</th>
      <th>venue</th>
      <th>city</th>
      <th>state</th>
      <th>attendance</th>
      <th>team_winner</th>
      <th>winning_pts</th>
      <th>qb_winner_1</th>
      <th>qb_winner_2</th>
      <th>coach_winner</th>
      <th>team_loser</th>
      <th>losing_pts</th>
      <th>qb_loser_1</th>
      <th>qb_loser_2</th>
      <th>coach_loser</th>
      <th>combined_pts</th>
      <th>difference_pts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2018-02-04</td>
      <td>52</td>
      <td>U.S. Bank Stadium</td>
      <td>Minneapolis</td>
      <td>Minnesota</td>
      <td>67612</td>
      <td>Philadelphia Eagles</td>
      <td>41</td>
      <td>Nick Foles</td>
      <td>NaN</td>
      <td>Doug Pederson</td>
      <td>New England Patriots</td>
      <td>33</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>74</td>
      <td>8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2017-02-05</td>
      <td>51</td>
      <td>NRG Stadium</td>
      <td>Houston</td>
      <td>Texas</td>
      <td>70807</td>
      <td>New England Patriots</td>
      <td>34</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>Atlanta Falcons</td>
      <td>28</td>
      <td>Matt Ryan</td>
      <td>NaN</td>
      <td>Dan Quinn</td>
      <td>62</td>
      <td>6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-02-07</td>
      <td>50</td>
      <td>Levi's Stadium</td>
      <td>Santa Clara</td>
      <td>California</td>
      <td>71088</td>
      <td>Denver Broncos</td>
      <td>24</td>
      <td>Peyton Manning</td>
      <td>NaN</td>
      <td>Gary Kubiak</td>
      <td>Carolina Panthers</td>
      <td>10</td>
      <td>Cam Newton</td>
      <td>NaN</td>
      <td>Ron Rivera</td>
      <td>34</td>
      <td>14</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2015-02-01</td>
      <td>49</td>
      <td>University of Phoenix Stadium</td>
      <td>Glendale</td>
      <td>Arizona</td>
      <td>70288</td>
      <td>New England Patriots</td>
      <td>28</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>Seattle Seahawks</td>
      <td>24</td>
      <td>Russell Wilson</td>
      <td>NaN</td>
      <td>Pete Carroll</td>
      <td>52</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014-02-02</td>
      <td>48</td>
      <td>MetLife Stadium</td>
      <td>East Rutherford</td>
      <td>New Jersey</td>
      <td>82529</td>
      <td>Seattle Seahawks</td>
      <td>43</td>
      <td>Russell Wilson</td>
      <td>NaN</td>
      <td>Pete Carroll</td>
      <td>Denver Broncos</td>
      <td>8</td>
      <td>Peyton Manning</td>
      <td>NaN</td>
      <td>John Fox</td>
      <td>51</td>
      <td>35</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2013-02-03</td>
      <td>47</td>
      <td>Mercedes-Benz Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>71024</td>
      <td>Baltimore Ravens</td>
      <td>34</td>
      <td>Joe Flacco</td>
      <td>NaN</td>
      <td>John Harbaugh</td>
      <td>San Francisco 49ers</td>
      <td>31</td>
      <td>Colin Kaepernick</td>
      <td>NaN</td>
      <td>Jim Harbaugh</td>
      <td>65</td>
      <td>3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2012-02-05</td>
      <td>46</td>
      <td>Lucas Oil Stadium</td>
      <td>Indianapolis</td>
      <td>Indiana</td>
      <td>68658</td>
      <td>New York Giants</td>
      <td>21</td>
      <td>Eli Manning</td>
      <td>NaN</td>
      <td>Tom Coughlin</td>
      <td>New England Patriots</td>
      <td>17</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>38</td>
      <td>4</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2011-02-06</td>
      <td>45</td>
      <td>Cowboys Stadium</td>
      <td>Arlington</td>
      <td>Texas</td>
      <td>103219</td>
      <td>Green Bay Packers</td>
      <td>31</td>
      <td>Aaron Rodgers</td>
      <td>NaN</td>
      <td>Mike McCarthy</td>
      <td>Pittsburgh Steelers</td>
      <td>25</td>
      <td>Ben Roethlisberger</td>
      <td>NaN</td>
      <td>Mike Tomlin</td>
      <td>56</td>
      <td>6</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2010-02-07</td>
      <td>44</td>
      <td>Sun Life Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>74059</td>
      <td>New Orleans Saints</td>
      <td>31</td>
      <td>Drew Brees</td>
      <td>NaN</td>
      <td>Sean Payton</td>
      <td>Indianapolis Colts</td>
      <td>17</td>
      <td>Peyton Manning</td>
      <td>NaN</td>
      <td>Jim Caldwell</td>
      <td>48</td>
      <td>14</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2009-02-01</td>
      <td>43</td>
      <td>Raymond James Stadium</td>
      <td>Tampa</td>
      <td>Florida</td>
      <td>70774</td>
      <td>Pittsburgh Steelers</td>
      <td>27</td>
      <td>Ben Roethlisberger</td>
      <td>NaN</td>
      <td>Mike Tomlin</td>
      <td>Arizona Cardinals</td>
      <td>23</td>
      <td>Kurt Warner</td>
      <td>NaN</td>
      <td>Ken Whisenhunt</td>
      <td>50</td>
      <td>4</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2008-02-03</td>
      <td>42</td>
      <td>University of Phoenix Stadium</td>
      <td>Glendale</td>
      <td>Arizona</td>
      <td>71101</td>
      <td>New York Giants</td>
      <td>17</td>
      <td>Eli Manning</td>
      <td>NaN</td>
      <td>Tom Coughlin</td>
      <td>New England Patriots</td>
      <td>14</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>31</td>
      <td>3</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2007-02-04</td>
      <td>41</td>
      <td>Dolphin Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>74512</td>
      <td>Indianapolis Colts</td>
      <td>29</td>
      <td>Peyton Manning</td>
      <td>NaN</td>
      <td>Tony Dungy</td>
      <td>Chicago Bears</td>
      <td>17</td>
      <td>Rex Grossman</td>
      <td>NaN</td>
      <td>Lovie Smith</td>
      <td>46</td>
      <td>12</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2006-02-05</td>
      <td>40</td>
      <td>Ford Field</td>
      <td>Detroit</td>
      <td>Michigan</td>
      <td>68206</td>
      <td>Pittsburgh Steelers</td>
      <td>21</td>
      <td>Ben Roethlisberger</td>
      <td>NaN</td>
      <td>Bill Cowher</td>
      <td>Seattle Seahawks</td>
      <td>10</td>
      <td>Matt Hasselbeck</td>
      <td>NaN</td>
      <td>Mike Holmgren</td>
      <td>31</td>
      <td>11</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2005-02-06</td>
      <td>39</td>
      <td>Alltel Stadium</td>
      <td>Jacksonville</td>
      <td>Florida</td>
      <td>78125</td>
      <td>New England Patriots</td>
      <td>24</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>Philadelphia Eagles</td>
      <td>21</td>
      <td>Donovan McNabb</td>
      <td>NaN</td>
      <td>Andy Reid</td>
      <td>45</td>
      <td>3</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2004-02-01</td>
      <td>38</td>
      <td>Reliant Stadium</td>
      <td>Houston</td>
      <td>Texas</td>
      <td>71525</td>
      <td>New England Patriots</td>
      <td>32</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>Carolina Panthers</td>
      <td>29</td>
      <td>Jake Delhomme</td>
      <td>NaN</td>
      <td>John Fox</td>
      <td>61</td>
      <td>3</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2003-01-26</td>
      <td>37</td>
      <td>Qualcomm Stadium</td>
      <td>San Diego</td>
      <td>California</td>
      <td>67603</td>
      <td>Tampa Bay Buccaneers</td>
      <td>48</td>
      <td>Brad Johnson</td>
      <td>NaN</td>
      <td>Jon Gruden</td>
      <td>Oakland Raiders</td>
      <td>21</td>
      <td>Rich Gannon</td>
      <td>NaN</td>
      <td>Bill Callahan</td>
      <td>69</td>
      <td>27</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2002-02-03</td>
      <td>36</td>
      <td>Louisiana Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>72922</td>
      <td>New England Patriots</td>
      <td>20</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>St. Louis Rams</td>
      <td>17</td>
      <td>Kurt Warner</td>
      <td>NaN</td>
      <td>Mike Martz</td>
      <td>37</td>
      <td>3</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2001-01-28</td>
      <td>35</td>
      <td>Raymond James Stadium</td>
      <td>Tampa</td>
      <td>Florida</td>
      <td>71921</td>
      <td>Baltimore Ravens</td>
      <td>34</td>
      <td>Trent Dilfer</td>
      <td>NaN</td>
      <td>Brian Billick</td>
      <td>New York Giants</td>
      <td>7</td>
      <td>Kerry Collins</td>
      <td>NaN</td>
      <td>Jim Fassel</td>
      <td>41</td>
      <td>27</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2000-01-30</td>
      <td>34</td>
      <td>Georgia Dome</td>
      <td>Atlanta</td>
      <td>Georgia</td>
      <td>72625</td>
      <td>St. Louis Rams</td>
      <td>23</td>
      <td>Kurt Warner</td>
      <td>NaN</td>
      <td>Dick Vermeil</td>
      <td>Tennessee Titans</td>
      <td>16</td>
      <td>Steve McNair</td>
      <td>NaN</td>
      <td>Jeff Fisher</td>
      <td>39</td>
      <td>7</td>
    </tr>
    <tr>
      <th>19</th>
      <td>1999-01-31</td>
      <td>33</td>
      <td>Pro Player Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>74803</td>
      <td>Denver Broncos</td>
      <td>34</td>
      <td>John Elway</td>
      <td>NaN</td>
      <td>Mike Shanahan</td>
      <td>Atlanta Falcons</td>
      <td>19</td>
      <td>Chris Chandler</td>
      <td>NaN</td>
      <td>Dan Reeves</td>
      <td>53</td>
      <td>15</td>
    </tr>
    <tr>
      <th>20</th>
      <td>1998-01-25</td>
      <td>32</td>
      <td>Qualcomm Stadium</td>
      <td>San Diego</td>
      <td>California</td>
      <td>68912</td>
      <td>Denver Broncos</td>
      <td>31</td>
      <td>John Elway</td>
      <td>NaN</td>
      <td>Mike Shanahan</td>
      <td>Green Bay Packers</td>
      <td>24</td>
      <td>Brett Favre</td>
      <td>NaN</td>
      <td>Mike Holmgren</td>
      <td>55</td>
      <td>7</td>
    </tr>
    <tr>
      <th>21</th>
      <td>1997-01-26</td>
      <td>31</td>
      <td>Louisiana Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>72301</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Brett Favre</td>
      <td>NaN</td>
      <td>Mike Holmgren</td>
      <td>New England Patriots</td>
      <td>21</td>
      <td>Drew Bledsoe</td>
      <td>NaN</td>
      <td>Bill Parcells</td>
      <td>56</td>
      <td>14</td>
    </tr>
    <tr>
      <th>22</th>
      <td>1996-01-28</td>
      <td>30</td>
      <td>Sun Devil Stadium</td>
      <td>Tempe</td>
      <td>Arizona</td>
      <td>76347</td>
      <td>Dallas Cowboys</td>
      <td>27</td>
      <td>Troy Aikman</td>
      <td>NaN</td>
      <td>Barry Switzer</td>
      <td>Pittsburgh Steelers</td>
      <td>17</td>
      <td>Neil O'Donnell</td>
      <td>NaN</td>
      <td>Bill Cowher</td>
      <td>44</td>
      <td>10</td>
    </tr>
    <tr>
      <th>23</th>
      <td>1995-01-29</td>
      <td>29</td>
      <td>Joe Robbie Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>74107</td>
      <td>San Francisco 49ers</td>
      <td>49</td>
      <td>Steve Young</td>
      <td>NaN</td>
      <td>George Seifert</td>
      <td>San Diego Chargers</td>
      <td>26</td>
      <td>Stan Humphreys</td>
      <td>NaN</td>
      <td>Bobby Ross</td>
      <td>75</td>
      <td>23</td>
    </tr>
    <tr>
      <th>24</th>
      <td>1994-01-30</td>
      <td>28</td>
      <td>Georgia Dome</td>
      <td>Atlanta</td>
      <td>Georgia</td>
      <td>72817</td>
      <td>Dallas Cowboys</td>
      <td>30</td>
      <td>Troy Aikman</td>
      <td>NaN</td>
      <td>Jimmy Johnson</td>
      <td>Buffalo Bills</td>
      <td>13</td>
      <td>Jim Kelly</td>
      <td>NaN</td>
      <td>Marv Levy</td>
      <td>43</td>
      <td>17</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1993-01-31</td>
      <td>27</td>
      <td>Rose Bowl</td>
      <td>Pasadena</td>
      <td>California</td>
      <td>98374</td>
      <td>Dallas Cowboys</td>
      <td>52</td>
      <td>Troy Aikman</td>
      <td>NaN</td>
      <td>Jimmy Johnson</td>
      <td>Buffalo Bills</td>
      <td>17</td>
      <td>Jim Kelly</td>
      <td>Frank Reich</td>
      <td>Marv Levy</td>
      <td>69</td>
      <td>35</td>
    </tr>
    <tr>
      <th>26</th>
      <td>1992-01-26</td>
      <td>26</td>
      <td>Metrodome</td>
      <td>Minneapolis</td>
      <td>Minnesota</td>
      <td>63130</td>
      <td>Washington Redskins</td>
      <td>37</td>
      <td>Mark Rypien</td>
      <td>NaN</td>
      <td>Joe Gibbs</td>
      <td>Buffalo Bills</td>
      <td>24</td>
      <td>Jim Kelly</td>
      <td>NaN</td>
      <td>Marv Levy</td>
      <td>61</td>
      <td>13</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1991-01-27</td>
      <td>25</td>
      <td>Tampa Stadium</td>
      <td>Tampa</td>
      <td>Florida</td>
      <td>73813</td>
      <td>New York Giants</td>
      <td>20</td>
      <td>Jeff Hostetler</td>
      <td>NaN</td>
      <td>Bill Parcells</td>
      <td>Buffalo Bills</td>
      <td>19</td>
      <td>Jim Kelly</td>
      <td>NaN</td>
      <td>Marv Levy</td>
      <td>39</td>
      <td>1</td>
    </tr>
    <tr>
      <th>28</th>
      <td>1990-01-28</td>
      <td>24</td>
      <td>Louisiana Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>72919</td>
      <td>San Francisco 49ers</td>
      <td>55</td>
      <td>Joe Montana</td>
      <td>NaN</td>
      <td>George Seifert</td>
      <td>Denver Broncos</td>
      <td>10</td>
      <td>John Elway</td>
      <td>NaN</td>
      <td>Dan Reeves</td>
      <td>65</td>
      <td>45</td>
    </tr>
    <tr>
      <th>29</th>
      <td>1989-01-22</td>
      <td>23</td>
      <td>Joe Robbie Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>75129</td>
      <td>San Francisco 49ers</td>
      <td>20</td>
      <td>Joe Montana</td>
      <td>NaN</td>
      <td>Bill Walsh</td>
      <td>Cincinnati Bengals</td>
      <td>16</td>
      <td>Boomer Esiason</td>
      <td>NaN</td>
      <td>Sam Wyche</td>
      <td>36</td>
      <td>4</td>
    </tr>
    <tr>
      <th>30</th>
      <td>1988-01-31</td>
      <td>22</td>
      <td>Jack Murphy Stadium</td>
      <td>San Diego</td>
      <td>California</td>
      <td>73302</td>
      <td>Washington Redskins</td>
      <td>42</td>
      <td>Doug Williams</td>
      <td>NaN</td>
      <td>Joe Gibbs</td>
      <td>Denver Broncos</td>
      <td>10</td>
      <td>John Elway</td>
      <td>NaN</td>
      <td>Dan Reeves</td>
      <td>52</td>
      <td>32</td>
    </tr>
    <tr>
      <th>31</th>
      <td>1987-01-25</td>
      <td>21</td>
      <td>Rose Bowl</td>
      <td>Pasadena</td>
      <td>California</td>
      <td>101063</td>
      <td>New York Giants</td>
      <td>39</td>
      <td>Phil Simms</td>
      <td>NaN</td>
      <td>Bill Parcells</td>
      <td>Denver Broncos</td>
      <td>20</td>
      <td>John Elway</td>
      <td>NaN</td>
      <td>Dan Reeves</td>
      <td>59</td>
      <td>19</td>
    </tr>
    <tr>
      <th>32</th>
      <td>1986-01-26</td>
      <td>20</td>
      <td>Louisiana Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>73818</td>
      <td>Chicago Bears</td>
      <td>46</td>
      <td>Jim McMahon</td>
      <td>NaN</td>
      <td>Mike Ditka</td>
      <td>New England Patriots</td>
      <td>10</td>
      <td>Tony Eason</td>
      <td>Steve Grogan</td>
      <td>Raymond Berry</td>
      <td>56</td>
      <td>36</td>
    </tr>
    <tr>
      <th>33</th>
      <td>1985-01-20</td>
      <td>19</td>
      <td>Stanford Stadium</td>
      <td>Palo Alto</td>
      <td>California</td>
      <td>84059</td>
      <td>San Francisco 49ers</td>
      <td>38</td>
      <td>Joe Montano</td>
      <td>NaN</td>
      <td>Bill Walsh</td>
      <td>Miami Dolphins</td>
      <td>16</td>
      <td>Dan Marino</td>
      <td>NaN</td>
      <td>Don Shula</td>
      <td>54</td>
      <td>22</td>
    </tr>
    <tr>
      <th>34</th>
      <td>1984-01-22</td>
      <td>18</td>
      <td>Tampa Stadium</td>
      <td>Tampa</td>
      <td>Florida</td>
      <td>72920</td>
      <td>Los Angeles Raiders</td>
      <td>38</td>
      <td>Jim Plunkett</td>
      <td>NaN</td>
      <td>Tom Flores</td>
      <td>Washington Redskins</td>
      <td>9</td>
      <td>Joe Theismann</td>
      <td>NaN</td>
      <td>Joe Gibbs</td>
      <td>47</td>
      <td>29</td>
    </tr>
    <tr>
      <th>35</th>
      <td>1983-01-30</td>
      <td>17</td>
      <td>Rose Bowl</td>
      <td>Pasadena</td>
      <td>California</td>
      <td>103667</td>
      <td>Washington Redskins</td>
      <td>27</td>
      <td>Joe Theismann</td>
      <td>NaN</td>
      <td>Joe Gibbs</td>
      <td>Miami Dolphins</td>
      <td>17</td>
      <td>David Woodley</td>
      <td>NaN</td>
      <td>Don Shula</td>
      <td>44</td>
      <td>10</td>
    </tr>
    <tr>
      <th>36</th>
      <td>1982-01-24</td>
      <td>16</td>
      <td>Pontiac Silverdome</td>
      <td>Pontiac</td>
      <td>Michigan</td>
      <td>81270</td>
      <td>San Francisco 49ers</td>
      <td>26</td>
      <td>Joe Montana</td>
      <td>NaN</td>
      <td>Bill Walsh</td>
      <td>Cincinnati Bengals</td>
      <td>21</td>
      <td>Ken Anderson</td>
      <td>NaN</td>
      <td>Forrest Gregg</td>
      <td>47</td>
      <td>5</td>
    </tr>
    <tr>
      <th>37</th>
      <td>1981-01-25</td>
      <td>15</td>
      <td>Louisiana Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>76135</td>
      <td>Oakland Raiders</td>
      <td>27</td>
      <td>Jim Plunkett</td>
      <td>NaN</td>
      <td>Tom Flores</td>
      <td>Philadelphia Eagles</td>
      <td>10</td>
      <td>Ron Jaworski</td>
      <td>NaN</td>
      <td>Dick Vermeil</td>
      <td>37</td>
      <td>17</td>
    </tr>
    <tr>
      <th>38</th>
      <td>1980-01-20</td>
      <td>14</td>
      <td>Rose Bowl</td>
      <td>Pasadena</td>
      <td>California</td>
      <td>103985</td>
      <td>Pittsburgh Steelers</td>
      <td>31</td>
      <td>Terry Bradshaw</td>
      <td>NaN</td>
      <td>Chuck Noll</td>
      <td>Los Angeles Rams</td>
      <td>19</td>
      <td>Vince Ferragamo</td>
      <td>NaN</td>
      <td>Ray Malavasi</td>
      <td>50</td>
      <td>12</td>
    </tr>
    <tr>
      <th>39</th>
      <td>1979-01-21</td>
      <td>13</td>
      <td>Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>79484</td>
      <td>Pittsburgh Steelers</td>
      <td>35</td>
      <td>Terry Bradshaw</td>
      <td>NaN</td>
      <td>Chuck Noll</td>
      <td>Dallas Cowboys</td>
      <td>31</td>
      <td>Roger Staubach</td>
      <td>NaN</td>
      <td>Tom Landry</td>
      <td>66</td>
      <td>4</td>
    </tr>
    <tr>
      <th>40</th>
      <td>1978-01-15</td>
      <td>12</td>
      <td>Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>76400</td>
      <td>Dallas Cowboys</td>
      <td>27</td>
      <td>Roger Staubach</td>
      <td>NaN</td>
      <td>Tom Landry</td>
      <td>Denver Broncos</td>
      <td>10</td>
      <td>Craig Morton</td>
      <td>NaN</td>
      <td>Red Miller</td>
      <td>37</td>
      <td>17</td>
    </tr>
    <tr>
      <th>41</th>
      <td>1977-01-09</td>
      <td>11</td>
      <td>Rose Bowl</td>
      <td>Pasadena</td>
      <td>California</td>
      <td>103438</td>
      <td>Oakland Raiders</td>
      <td>32</td>
      <td>Kenny Stabler</td>
      <td>NaN</td>
      <td>John Madden</td>
      <td>Minnesota Vikings</td>
      <td>14</td>
      <td>Fran Tarkenton</td>
      <td>NaN</td>
      <td>Bud Grant</td>
      <td>46</td>
      <td>18</td>
    </tr>
    <tr>
      <th>42</th>
      <td>1976-01-18</td>
      <td>10</td>
      <td>Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>80187</td>
      <td>Pittsburgh Steelers</td>
      <td>21</td>
      <td>Terry Bradshaw</td>
      <td>NaN</td>
      <td>Chuck Noll</td>
      <td>Dallas Cowboys</td>
      <td>17</td>
      <td>Roger Staubach</td>
      <td>NaN</td>
      <td>Tom Landry</td>
      <td>38</td>
      <td>4</td>
    </tr>
    <tr>
      <th>43</th>
      <td>1975-01-12</td>
      <td>9</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80997</td>
      <td>Pittsburgh Steelers</td>
      <td>16</td>
      <td>Terry Bradshaw</td>
      <td>NaN</td>
      <td>Chuck Noll</td>
      <td>Minnesota Vikings</td>
      <td>6</td>
      <td>Fran Tarkenton</td>
      <td>NaN</td>
      <td>Bud Grant</td>
      <td>22</td>
      <td>10</td>
    </tr>
    <tr>
      <th>44</th>
      <td>1974-01-13</td>
      <td>8</td>
      <td>Rice Stadium</td>
      <td>Houston</td>
      <td>Texas</td>
      <td>71882</td>
      <td>Miami Dolphins</td>
      <td>24</td>
      <td>Bob Griese</td>
      <td>NaN</td>
      <td>Don Shula</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Fran Tarkenton</td>
      <td>NaN</td>
      <td>Bud Grant</td>
      <td>31</td>
      <td>17</td>
    </tr>
    <tr>
      <th>45</th>
      <td>1973-01-14</td>
      <td>7</td>
      <td>Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>90182</td>
      <td>Miami Dolphins</td>
      <td>14</td>
      <td>Bob Griese</td>
      <td>NaN</td>
      <td>Don Shula</td>
      <td>Washington Redskins</td>
      <td>7</td>
      <td>Bill Kilmer</td>
      <td>NaN</td>
      <td>George Allen</td>
      <td>21</td>
      <td>7</td>
    </tr>
    <tr>
      <th>46</th>
      <td>1972-01-16</td>
      <td>6</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>81023</td>
      <td>Dallas Cowboys</td>
      <td>24</td>
      <td>Roger Staubach</td>
      <td>NaN</td>
      <td>Tom Landry</td>
      <td>Miami Dolphins</td>
      <td>3</td>
      <td>Bob Griese</td>
      <td>NaN</td>
      <td>Don Shula</td>
      <td>27</td>
      <td>21</td>
    </tr>
    <tr>
      <th>47</th>
      <td>1971-01-17</td>
      <td>5</td>
      <td>Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>79204</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Earl Morrall</td>
      <td>Johnny Unitas</td>
      <td>Don McCafferty</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Craig Morton</td>
      <td>NaN</td>
      <td>Tom Landry</td>
      <td>29</td>
      <td>3</td>
    </tr>
    <tr>
      <th>48</th>
      <td>1970-01-11</td>
      <td>4</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80562</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Len Dawson</td>
      <td>Mike Livingston</td>
      <td>Hank Stram</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Joe Kapp</td>
      <td>NaN</td>
      <td>Bud Grant</td>
      <td>30</td>
      <td>16</td>
    </tr>
    <tr>
      <th>49</th>
      <td>1969-01-12</td>
      <td>3</td>
      <td>Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75389</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Joe Namath</td>
      <td>NaN</td>
      <td>Weeb Ewbank</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Earl Morrall</td>
      <td>Johnny Unitas</td>
      <td>Don Shula</td>
      <td>23</td>
      <td>9</td>
    </tr>
    <tr>
      <th>50</th>
      <td>1968-01-14</td>
      <td>2</td>
      <td>Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75546</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Bart Starr</td>
      <td>NaN</td>
      <td>Vince Lombardi</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Daryle Lamonica</td>
      <td>NaN</td>
      <td>John Rauch</td>
      <td>47</td>
      <td>19</td>
    </tr>
    <tr>
      <th>51</th>
      <td>1967-01-15</td>
      <td>1</td>
      <td>Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>61946</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Bart Starr</td>
      <td>NaN</td>
      <td>Vince Lombardi</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Len Dawson</td>
      <td>NaN</td>
      <td>Hank Stram</td>
      <td>45</td>
      <td>25</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>super_bowl</th>
      <th>network</th>
      <th>avg_us_viewers</th>
      <th>total_us_viewers</th>
      <th>rating_household</th>
      <th>share_household</th>
      <th>rating_18_49</th>
      <th>share_18_49</th>
      <th>ad_cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>52</td>
      <td>NBC</td>
      <td>103390000</td>
      <td>NaN</td>
      <td>43.1</td>
      <td>68</td>
      <td>33.4</td>
      <td>78.0</td>
      <td>5000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>51</td>
      <td>Fox</td>
      <td>111319000</td>
      <td>172000000.0</td>
      <td>45.3</td>
      <td>73</td>
      <td>37.1</td>
      <td>79.0</td>
      <td>5000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>50</td>
      <td>CBS</td>
      <td>111864000</td>
      <td>167000000.0</td>
      <td>46.6</td>
      <td>72</td>
      <td>37.7</td>
      <td>79.0</td>
      <td>5000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>49</td>
      <td>NBC</td>
      <td>114442000</td>
      <td>168000000.0</td>
      <td>47.5</td>
      <td>71</td>
      <td>39.1</td>
      <td>79.0</td>
      <td>4500000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>48</td>
      <td>Fox</td>
      <td>112191000</td>
      <td>167000000.0</td>
      <td>46.7</td>
      <td>69</td>
      <td>39.3</td>
      <td>77.0</td>
      <td>4000000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>47</td>
      <td>CBS</td>
      <td>108693000</td>
      <td>164100000.0</td>
      <td>46.3</td>
      <td>69</td>
      <td>39.7</td>
      <td>77.0</td>
      <td>4000000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>46</td>
      <td>NBC</td>
      <td>111346000</td>
      <td>163500000.0</td>
      <td>47.0</td>
      <td>71</td>
      <td>40.5</td>
      <td>NaN</td>
      <td>3500000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>45</td>
      <td>Fox</td>
      <td>111041000</td>
      <td>162900000.0</td>
      <td>46.0</td>
      <td>69</td>
      <td>39.9</td>
      <td>NaN</td>
      <td>3100000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>44</td>
      <td>CBS</td>
      <td>106476000</td>
      <td>153400000.0</td>
      <td>45.0</td>
      <td>68</td>
      <td>38.6</td>
      <td>NaN</td>
      <td>2800000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>43</td>
      <td>NBC</td>
      <td>98732000</td>
      <td>151600000.0</td>
      <td>42.0</td>
      <td>64</td>
      <td>36.7</td>
      <td>NaN</td>
      <td>3000000</td>
    </tr>
    <tr>
      <th>10</th>
      <td>42</td>
      <td>Fox</td>
      <td>97448000</td>
      <td>148300000.0</td>
      <td>43.1</td>
      <td>65</td>
      <td>37.5</td>
      <td>NaN</td>
      <td>2699963</td>
    </tr>
    <tr>
      <th>11</th>
      <td>41</td>
      <td>CBS</td>
      <td>93184000</td>
      <td>139800000.0</td>
      <td>42.6</td>
      <td>64</td>
      <td>35.2</td>
      <td>NaN</td>
      <td>2385365</td>
    </tr>
    <tr>
      <th>12</th>
      <td>40</td>
      <td>ABC</td>
      <td>90745000</td>
      <td>141400000.0</td>
      <td>41.6</td>
      <td>62</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2500000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>39</td>
      <td>Fox</td>
      <td>86072000</td>
      <td>NaN</td>
      <td>41.1</td>
      <td>62</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2400000</td>
    </tr>
    <tr>
      <th>14</th>
      <td>38</td>
      <td>CBS</td>
      <td>89795000</td>
      <td>144400000.0</td>
      <td>41.4</td>
      <td>63</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2302200</td>
    </tr>
    <tr>
      <th>15</th>
      <td>37</td>
      <td>ABC</td>
      <td>88637000</td>
      <td>138500000.0</td>
      <td>40.7</td>
      <td>61</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2200000</td>
    </tr>
    <tr>
      <th>16</th>
      <td>36</td>
      <td>Fox</td>
      <td>86801000</td>
      <td>NaN</td>
      <td>40.4</td>
      <td>61</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2200000</td>
    </tr>
    <tr>
      <th>17</th>
      <td>35</td>
      <td>CBS</td>
      <td>84335000</td>
      <td>NaN</td>
      <td>40.4</td>
      <td>61</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2200000</td>
    </tr>
    <tr>
      <th>18</th>
      <td>34</td>
      <td>ABC</td>
      <td>88465000</td>
      <td>NaN</td>
      <td>43.3</td>
      <td>63</td>
      <td>37.9</td>
      <td>NaN</td>
      <td>2100000</td>
    </tr>
    <tr>
      <th>19</th>
      <td>33</td>
      <td>Fox</td>
      <td>83720000</td>
      <td>NaN</td>
      <td>40.2</td>
      <td>61</td>
      <td>36.4</td>
      <td>NaN</td>
      <td>1600000</td>
    </tr>
    <tr>
      <th>20</th>
      <td>32</td>
      <td>NBC</td>
      <td>90000000</td>
      <td>NaN</td>
      <td>44.5</td>
      <td>67</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1291100</td>
    </tr>
    <tr>
      <th>21</th>
      <td>31</td>
      <td>Fox</td>
      <td>87870000</td>
      <td>NaN</td>
      <td>43.3</td>
      <td>65</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1200000</td>
    </tr>
    <tr>
      <th>22</th>
      <td>30</td>
      <td>NBC</td>
      <td>94080000</td>
      <td>NaN</td>
      <td>46.0</td>
      <td>68</td>
      <td>41.2</td>
      <td>NaN</td>
      <td>1085000</td>
    </tr>
    <tr>
      <th>23</th>
      <td>29</td>
      <td>ABC</td>
      <td>83420000</td>
      <td>NaN</td>
      <td>41.3</td>
      <td>62</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1150000</td>
    </tr>
    <tr>
      <th>24</th>
      <td>28</td>
      <td>NBC</td>
      <td>90000000</td>
      <td>NaN</td>
      <td>45.5</td>
      <td>66</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>900000</td>
    </tr>
    <tr>
      <th>25</th>
      <td>27</td>
      <td>NBC</td>
      <td>90990000</td>
      <td>NaN</td>
      <td>45.1</td>
      <td>66</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>850000</td>
    </tr>
    <tr>
      <th>26</th>
      <td>26</td>
      <td>CBS</td>
      <td>79590000</td>
      <td>NaN</td>
      <td>40.3</td>
      <td>61</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>850000</td>
    </tr>
    <tr>
      <th>27</th>
      <td>25</td>
      <td>ABC</td>
      <td>79510000</td>
      <td>NaN</td>
      <td>41.9</td>
      <td>63</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>800000</td>
    </tr>
    <tr>
      <th>28</th>
      <td>24</td>
      <td>CBS</td>
      <td>73852000</td>
      <td>NaN</td>
      <td>39.0</td>
      <td>67</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>700400</td>
    </tr>
    <tr>
      <th>29</th>
      <td>23</td>
      <td>NBC</td>
      <td>81590000</td>
      <td>NaN</td>
      <td>43.5</td>
      <td>68</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>675000</td>
    </tr>
    <tr>
      <th>30</th>
      <td>22</td>
      <td>ABC</td>
      <td>80140000</td>
      <td>NaN</td>
      <td>41.9</td>
      <td>62</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>645000</td>
    </tr>
    <tr>
      <th>31</th>
      <td>21</td>
      <td>CBS</td>
      <td>87190000</td>
      <td>NaN</td>
      <td>45.8</td>
      <td>66</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>600000</td>
    </tr>
    <tr>
      <th>32</th>
      <td>20</td>
      <td>NBC</td>
      <td>92570000</td>
      <td>NaN</td>
      <td>48.3</td>
      <td>70</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>550000</td>
    </tr>
    <tr>
      <th>33</th>
      <td>19</td>
      <td>ABC</td>
      <td>85530000</td>
      <td>NaN</td>
      <td>46.4</td>
      <td>63</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>525000</td>
    </tr>
    <tr>
      <th>34</th>
      <td>18</td>
      <td>CBS</td>
      <td>77620000</td>
      <td>NaN</td>
      <td>46.4</td>
      <td>71</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>368200</td>
    </tr>
    <tr>
      <th>35</th>
      <td>17</td>
      <td>NBC</td>
      <td>81770000</td>
      <td>NaN</td>
      <td>48.6</td>
      <td>69</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>400000</td>
    </tr>
    <tr>
      <th>36</th>
      <td>16</td>
      <td>CBS</td>
      <td>85240000</td>
      <td>NaN</td>
      <td>49.1</td>
      <td>73</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>324300</td>
    </tr>
    <tr>
      <th>37</th>
      <td>15</td>
      <td>NBC</td>
      <td>68290000</td>
      <td>NaN</td>
      <td>44.4</td>
      <td>63</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>275000</td>
    </tr>
    <tr>
      <th>38</th>
      <td>14</td>
      <td>CBS</td>
      <td>76240000</td>
      <td>NaN</td>
      <td>46.3</td>
      <td>67</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>222000</td>
    </tr>
    <tr>
      <th>39</th>
      <td>13</td>
      <td>NBC</td>
      <td>74740000</td>
      <td>NaN</td>
      <td>47.1</td>
      <td>74</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>185000</td>
    </tr>
    <tr>
      <th>40</th>
      <td>12</td>
      <td>CBS</td>
      <td>78940000</td>
      <td>NaN</td>
      <td>47.2</td>
      <td>67</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>162300</td>
    </tr>
    <tr>
      <th>41</th>
      <td>11</td>
      <td>NBC</td>
      <td>62050000</td>
      <td>NaN</td>
      <td>44.4</td>
      <td>73</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>125000</td>
    </tr>
    <tr>
      <th>42</th>
      <td>10</td>
      <td>CBS</td>
      <td>57710000</td>
      <td>NaN</td>
      <td>42.3</td>
      <td>78</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>110000</td>
    </tr>
    <tr>
      <th>43</th>
      <td>9</td>
      <td>NBC</td>
      <td>56050000</td>
      <td>NaN</td>
      <td>42.4</td>
      <td>72</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>107000</td>
    </tr>
    <tr>
      <th>44</th>
      <td>8</td>
      <td>CBS</td>
      <td>51700000</td>
      <td>NaN</td>
      <td>41.6</td>
      <td>73</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>103500</td>
    </tr>
    <tr>
      <th>45</th>
      <td>7</td>
      <td>NBC</td>
      <td>53320000</td>
      <td>NaN</td>
      <td>42.7</td>
      <td>72</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>88100</td>
    </tr>
    <tr>
      <th>46</th>
      <td>6</td>
      <td>CBS</td>
      <td>56640000</td>
      <td>NaN</td>
      <td>44.2</td>
      <td>74</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>86100</td>
    </tr>
    <tr>
      <th>47</th>
      <td>5</td>
      <td>NBC</td>
      <td>46040000</td>
      <td>NaN</td>
      <td>39.9</td>
      <td>75</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>72500</td>
    </tr>
    <tr>
      <th>48</th>
      <td>4</td>
      <td>CBS</td>
      <td>44270000</td>
      <td>NaN</td>
      <td>39.4</td>
      <td>69</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>78200</td>
    </tr>
    <tr>
      <th>49</th>
      <td>3</td>
      <td>NBC</td>
      <td>41660000</td>
      <td>NaN</td>
      <td>36.0</td>
      <td>70</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>55000</td>
    </tr>
    <tr>
      <th>50</th>
      <td>2</td>
      <td>CBS</td>
      <td>39120000</td>
      <td>NaN</td>
      <td>36.8</td>
      <td>68</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>54500</td>
    </tr>
    <tr>
      <th>51</th>
      <td>1</td>
      <td>CBS</td>
      <td>26750000</td>
      <td>51180000.0</td>
      <td>22.6</td>
      <td>43</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>42500</td>
    </tr>
    <tr>
      <th>52</th>
      <td>1</td>
      <td>NBC</td>
      <td>24430000</td>
      <td>NaN</td>
      <td>18.5</td>
      <td>36</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>37500</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>super_bowl</th>
      <th>musician</th>
      <th>num_songs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>52</td>
      <td>Justin Timberlake</td>
      <td>11.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>52</td>
      <td>University of Minnesota Marching Band</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>51</td>
      <td>Lady Gaga</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>50</td>
      <td>Coldplay</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>50</td>
      <td>Beyoncé</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>50</td>
      <td>Bruno Mars</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>50</td>
      <td>Mark Ronson</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>50</td>
      <td>University of California Marching Band</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>50</td>
      <td>Youth Orchestra Los Angeles</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>50</td>
      <td>Gustavo Dudamel</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>49</td>
      <td>Katy Perry</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>49</td>
      <td>Lenny Kravitz</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>49</td>
      <td>Missy Elliott</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>49</td>
      <td>Arizona State University Sun Devil Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>14</th>
      <td>48</td>
      <td>Bruno Mars</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>48</td>
      <td>Red Hot Chili Peppers</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>47</td>
      <td>Beyoncé</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>47</td>
      <td>Destiny's Child</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>47</td>
      <td>Kelly Rowland</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>47</td>
      <td>Michelle Williams</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>46</td>
      <td>Madonna</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>46</td>
      <td>LMFAO</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>46</td>
      <td>Nicki Minaj</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>46</td>
      <td>M.I.A.</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>46</td>
      <td>Cee Lo Green</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>45</td>
      <td>The Black Eyed Peas</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>45</td>
      <td>Slash</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>45</td>
      <td>Usher</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>45</td>
      <td>will.i.am</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>45</td>
      <td>Fergie</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>104</th>
      <td>14</td>
      <td>Up with People</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>105</th>
      <td>14</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>106</th>
      <td>13</td>
      <td>Ken Hamilton</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>107</th>
      <td>13</td>
      <td>Gramacks</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>108</th>
      <td>12</td>
      <td>Tyler Junior College Apache Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>109</th>
      <td>12</td>
      <td>Pete Fountain</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>110</th>
      <td>12</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>111</th>
      <td>11</td>
      <td>Los Angeles Unified School District All City H...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>112</th>
      <td>10</td>
      <td>Up with People</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>113</th>
      <td>9</td>
      <td>Mercer Ellington</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>114</th>
      <td>9</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>115</th>
      <td>8</td>
      <td>University of Texas Longhorn Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>116</th>
      <td>8</td>
      <td>Judy Mallett</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>117</th>
      <td>7</td>
      <td>University of Michigan Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>118</th>
      <td>7</td>
      <td>Woody Herman</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>119</th>
      <td>7</td>
      <td>Andy Williams</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>120</th>
      <td>6</td>
      <td>Ella Fitzgerald</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>121</th>
      <td>6</td>
      <td>Carol Channing</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>122</th>
      <td>6</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>123</th>
      <td>6</td>
      <td>United States Air Force Academy Cadet Chorale</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>124</th>
      <td>5</td>
      <td>Southeast Missouri State Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>125</th>
      <td>4</td>
      <td>Marguerite Piazza</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>126</th>
      <td>4</td>
      <td>Doc Severinsen</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>127</th>
      <td>4</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>128</th>
      <td>4</td>
      <td>The Human Jukebox</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>129</th>
      <td>3</td>
      <td>Florida A&amp;M University Marching 100 Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>130</th>
      <td>2</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>131</th>
      <td>1</td>
      <td>University of Arizona Symphonic Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>132</th>
      <td>1</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>133</th>
      <td>1</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>134 rows × 3 columns</p>
</div>

---
## 2. Taking note of dataset issues
<p>For the Super Bowl game data, we can see the dataset appears whole except for missing values in the backup quarterback columns (<code>qb_winner_2</code> and <code>qb_loser_2</code>), which make sense given most starting QBs in the Super Bowl (<code>qb_winner_1</code> and <code>qb_loser_1</code>) play the entire game.</p>
<p>From the visual inspection of TV and halftime musicians data, there is only one missing value displayed, but I've got a hunch there are more. The Super Bowl goes all the way back to 1967, and the more granular columns (e.g. the number of songs for halftime musicians) probably weren't tracked reliably over time. Wikipedia is great but not perfect.</p>
<p>An inspection of the <code>.info()</code> output for <code>tv</code> and <code>halftime_musicians</code> shows us that there are multiple columns with null values.</p>


```python
# Summary of the TV data to inspect
tv.info()
print('\n')

# Summary of the halftime musician data to inspect
halftime_musicians.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 53 entries, 0 to 52
    Data columns (total 9 columns):
    super_bowl          53 non-null int64
    network             53 non-null object
    avg_us_viewers      53 non-null int64
    total_us_viewers    15 non-null float64
    rating_household    53 non-null float64
    share_household     53 non-null int64
    rating_18_49        15 non-null float64
    share_18_49         6 non-null float64
    ad_cost             53 non-null int64
    dtypes: float64(4), int64(4), object(1)
    memory usage: 3.8+ KB


    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 134 entries, 0 to 133
    Data columns (total 3 columns):
    super_bowl    134 non-null int64
    musician      134 non-null object
    num_songs     88 non-null float64
    dtypes: float64(1), int64(1), object(1)
    memory usage: 3.2+ KB

---
## 3. Combined points distribution
<p>For the TV data, the following columns have missing values and a lot of them:</p>
<ul>
<li><code>total_us_viewers</code> (amount of U.S. viewers who watched at least some part of the broadcast)</li>
<li><code>rating_18_49</code> (average % of U.S. adults 18-49 who live in a household with a TV that were watching for the entire broadcast)</li>
<li><code>share_18_49</code> (average % of U.S. adults 18-49 who live in a household with a TV <em>in use</em> that were watching for the entire broadcast)</li>
</ul>
<p>For the halftime musician data, there are missing numbers of songs performed (<code>num_songs</code>) for about a third of the performances.</p>
<p>There are a lot of potential reasons for these missing values. Was the data ever tracked? Was it lost in history? Is the research effort to make this data whole worth it? Maybe. Watching every Super Bowl halftime show to get song counts would be pretty fun. But we don't have the time to do that kind of stuff now -- the Big Game is less than a week away! Let's take note of where the dataset isn't perfect and start uncovering some insights.</p>
<p>Let's start by looking at combined points for each Super Bowl by visualizing the distribution. Let's also pinpoint the Super Bowls with the highest and lowest scores.</p>


```python
# Import matplotlib and set plotting style
from matplotlib import pyplot as plt
%matplotlib inline
plt.style.use('seaborn')
# Plot a histogram of combined points
super_bowls.combined_pts.hist()
plt.xlabel('Combined Points')
plt.ylabel('Number of Super Bowls')
plt.show()

# Display the Super Bowls with the highest and lowest combined scores
display(super_bowls[super_bowls['combined_pts'] > 70])
display(super_bowls[super_bowls['combined_pts'] < 25])
```


![png](/assets/img/superbowl-analysis_files/superbowl-analysis_5_0.png)



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
      <th>date</th>
      <th>super_bowl</th>
      <th>venue</th>
      <th>city</th>
      <th>state</th>
      <th>attendance</th>
      <th>team_winner</th>
      <th>winning_pts</th>
      <th>qb_winner_1</th>
      <th>qb_winner_2</th>
      <th>coach_winner</th>
      <th>team_loser</th>
      <th>losing_pts</th>
      <th>qb_loser_1</th>
      <th>qb_loser_2</th>
      <th>coach_loser</th>
      <th>combined_pts</th>
      <th>difference_pts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2018-02-04</td>
      <td>52</td>
      <td>U.S. Bank Stadium</td>
      <td>Minneapolis</td>
      <td>Minnesota</td>
      <td>67612</td>
      <td>Philadelphia Eagles</td>
      <td>41</td>
      <td>Nick Foles</td>
      <td>NaN</td>
      <td>Doug Pederson</td>
      <td>New England Patriots</td>
      <td>33</td>
      <td>Tom Brady</td>
      <td>NaN</td>
      <td>Bill Belichick</td>
      <td>74</td>
      <td>8</td>
    </tr>
    <tr>
      <th>23</th>
      <td>1995-01-29</td>
      <td>29</td>
      <td>Joe Robbie Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>74107</td>
      <td>San Francisco 49ers</td>
      <td>49</td>
      <td>Steve Young</td>
      <td>NaN</td>
      <td>George Seifert</td>
      <td>San Diego Chargers</td>
      <td>26</td>
      <td>Stan Humphreys</td>
      <td>NaN</td>
      <td>Bobby Ross</td>
      <td>75</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>date</th>
      <th>super_bowl</th>
      <th>venue</th>
      <th>city</th>
      <th>state</th>
      <th>attendance</th>
      <th>team_winner</th>
      <th>winning_pts</th>
      <th>qb_winner_1</th>
      <th>qb_winner_2</th>
      <th>coach_winner</th>
      <th>team_loser</th>
      <th>losing_pts</th>
      <th>qb_loser_1</th>
      <th>qb_loser_2</th>
      <th>coach_loser</th>
      <th>combined_pts</th>
      <th>difference_pts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>43</th>
      <td>1975-01-12</td>
      <td>9</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80997</td>
      <td>Pittsburgh Steelers</td>
      <td>16</td>
      <td>Terry Bradshaw</td>
      <td>NaN</td>
      <td>Chuck Noll</td>
      <td>Minnesota Vikings</td>
      <td>6</td>
      <td>Fran Tarkenton</td>
      <td>NaN</td>
      <td>Bud Grant</td>
      <td>22</td>
      <td>10</td>
    </tr>
    <tr>
      <th>45</th>
      <td>1973-01-14</td>
      <td>7</td>
      <td>Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>90182</td>
      <td>Miami Dolphins</td>
      <td>14</td>
      <td>Bob Griese</td>
      <td>NaN</td>
      <td>Don Shula</td>
      <td>Washington Redskins</td>
      <td>7</td>
      <td>Bill Kilmer</td>
      <td>NaN</td>
      <td>George Allen</td>
      <td>21</td>
      <td>7</td>
    </tr>
    <tr>
      <th>49</th>
      <td>1969-01-12</td>
      <td>3</td>
      <td>Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75389</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Joe Namath</td>
      <td>NaN</td>
      <td>Weeb Ewbank</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Earl Morrall</td>
      <td>Johnny Unitas</td>
      <td>Don Shula</td>
      <td>23</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

---
## 4. Point difference distribution
<p>Most combined scores are around 40-50 points, with the extremes being roughly equal distance away in opposite directions. Going up to the highest combined scores at 74 and 75, we find two games featuring dominant quarterback performances. One even happened recently in 2018's Super Bowl LII where Tom Brady's Patriots lost to Nick Foles' underdog Eagles 41-33 for a combined score of 74.</p>
<p>Going down to the lowest combined scores, we have Super Bowl III and VII, which featured tough defenses that dominated. We also have Super Bowl IX in New Orleans in 1975, whose 16-6 score can be attributed to inclement weather. The field was slick from overnight rain, and it was cold at 46 °F (8 °C), making it hard for the Steelers and Vikings to do much offensively. This was the second-coldest Super Bowl ever and the last to be played in inclement weather for over 30 years. The NFL realized people like points, I guess.</p>
<p>Let's take a look at point <em>difference</em> now.</p>


```python
# Plot a histogram of point differences
plt.hist(super_bowls.difference_pts)
plt.xlabel('Point Difference')
plt.ylabel('Number of Super Bowls')
plt.show()
# Display the closest game(s) and biggest blowouts
display(super_bowls[super_bowls.difference_pts == 1])
display(super_bowls[super_bowls.difference_pts >= 35])
```


![png](/assets/img/superbowl-analysis_files/superbowl-analysis_7_0.png)



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
      <th>date</th>
      <th>super_bowl</th>
      <th>venue</th>
      <th>city</th>
      <th>state</th>
      <th>attendance</th>
      <th>team_winner</th>
      <th>winning_pts</th>
      <th>qb_winner_1</th>
      <th>qb_winner_2</th>
      <th>coach_winner</th>
      <th>team_loser</th>
      <th>losing_pts</th>
      <th>qb_loser_1</th>
      <th>qb_loser_2</th>
      <th>coach_loser</th>
      <th>combined_pts</th>
      <th>difference_pts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>27</th>
      <td>1991-01-27</td>
      <td>25</td>
      <td>Tampa Stadium</td>
      <td>Tampa</td>
      <td>Florida</td>
      <td>73813</td>
      <td>New York Giants</td>
      <td>20</td>
      <td>Jeff Hostetler</td>
      <td>NaN</td>
      <td>Bill Parcells</td>
      <td>Buffalo Bills</td>
      <td>19</td>
      <td>Jim Kelly</td>
      <td>NaN</td>
      <td>Marv Levy</td>
      <td>39</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>date</th>
      <th>super_bowl</th>
      <th>venue</th>
      <th>city</th>
      <th>state</th>
      <th>attendance</th>
      <th>team_winner</th>
      <th>winning_pts</th>
      <th>qb_winner_1</th>
      <th>qb_winner_2</th>
      <th>coach_winner</th>
      <th>team_loser</th>
      <th>losing_pts</th>
      <th>qb_loser_1</th>
      <th>qb_loser_2</th>
      <th>coach_loser</th>
      <th>combined_pts</th>
      <th>difference_pts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>2014-02-02</td>
      <td>48</td>
      <td>MetLife Stadium</td>
      <td>East Rutherford</td>
      <td>New Jersey</td>
      <td>82529</td>
      <td>Seattle Seahawks</td>
      <td>43</td>
      <td>Russell Wilson</td>
      <td>NaN</td>
      <td>Pete Carroll</td>
      <td>Denver Broncos</td>
      <td>8</td>
      <td>Peyton Manning</td>
      <td>NaN</td>
      <td>John Fox</td>
      <td>51</td>
      <td>35</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1993-01-31</td>
      <td>27</td>
      <td>Rose Bowl</td>
      <td>Pasadena</td>
      <td>California</td>
      <td>98374</td>
      <td>Dallas Cowboys</td>
      <td>52</td>
      <td>Troy Aikman</td>
      <td>NaN</td>
      <td>Jimmy Johnson</td>
      <td>Buffalo Bills</td>
      <td>17</td>
      <td>Jim Kelly</td>
      <td>Frank Reich</td>
      <td>Marv Levy</td>
      <td>69</td>
      <td>35</td>
    </tr>
    <tr>
      <th>28</th>
      <td>1990-01-28</td>
      <td>24</td>
      <td>Louisiana Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>72919</td>
      <td>San Francisco 49ers</td>
      <td>55</td>
      <td>Joe Montana</td>
      <td>NaN</td>
      <td>George Seifert</td>
      <td>Denver Broncos</td>
      <td>10</td>
      <td>John Elway</td>
      <td>NaN</td>
      <td>Dan Reeves</td>
      <td>65</td>
      <td>45</td>
    </tr>
    <tr>
      <th>32</th>
      <td>1986-01-26</td>
      <td>20</td>
      <td>Louisiana Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>73818</td>
      <td>Chicago Bears</td>
      <td>46</td>
      <td>Jim McMahon</td>
      <td>NaN</td>
      <td>Mike Ditka</td>
      <td>New England Patriots</td>
      <td>10</td>
      <td>Tony Eason</td>
      <td>Steve Grogan</td>
      <td>Raymond Berry</td>
      <td>56</td>
      <td>36</td>
    </tr>
  </tbody>
</table>
</div>

---
## 5. Do blowouts translate to lost viewers?
<p>The vast majority of Super Bowls are close games. Makes sense. Both teams are likely to be deserving if they've made it this far. The closest game ever was when the Buffalo Bills lost to the New York Giants by 1 point in 1991, which was  best remembered for Scott Norwood's last-second missed field goal attempt that went <em><a href="https://www.youtube.com/watch?v=RPFZCGgjDSg">wide right</a></em>, kicking off four Bills Super Bowl losses in a row. Poor Scott. The biggest point discrepancy ever was 45 points (!) where Hall of Famer Joe Montana's led the San Francisco 49ers to victory in 1990, one year before the closest game ever.</p>
<p>I remember watching the Seahawks crush the Broncos by 35 points (43-8) in 2014, which sucked to watch in my opinion. The game was never really close. I'm pretty sure we changed the channel at the end of the third quarter. Let's combine our game data and TV to see if this is a universal phenomenon. Do large point differences translate to lost viewers? We can plot <a href="https://en.wikipedia.org/wiki/Nielsen_ratings">household share</a> <em>(average percentage of U.S. households with a TV in use that were watching for the entire broadcast)</em> vs. point difference to find out.</p>


```python
# Join game and TV data, filtering out SB I because it was split over two networks
games_tv = pd.merge(tv[tv['super_bowl'] > 1], super_bowls, on='super_bowl')

# Import seaborn
import seaborn as sns
# Create a scatter plot with a linear regression model fit
sns.regplot(x='difference_pts', y='share_household', data=games_tv)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f6d935b8240>




![png](/assets/img/superbowl-analysis_files/superbowl-analysis_9_1.png)

---
## 6. Viewership and the ad industry over time
<p>The downward sloping regression line and the 95% confidence interval for that regression <em>suggest</em> that bailing on the game if it is a blowout is common. Though it matches our intuition, we must take it with a grain of salt because the linear relationship in the data is weak due to our small sample size of 52 games.</p>
<p>Regardless of the score though, I bet most people stick it out for the halftime show, which is good news for the TV networks and advertisers. A 30-second spot costs a pretty <a href="https://www.businessinsider.com/super-bowl-commercials-cost-more-than-eagles-quarterback-earns-2018-1">\$5 million</a> now, but has it always been that way? And how have number of viewers and household ratings trended alongside ad cost? We can find out using line plots that share a "Super Bowl" x-axis.</p>


```python
# Create a figure with 3x1 subplot and activate the top subplot
plt.subplot(3, 1, 1)
plt.plot(tv['super_bowl'], tv['avg_us_viewers'], color='#648FFF')
plt.title('Average Number of US Viewers')

# Activate the middle subplot
plt.subplot(3, 1, 2)
plt.plot(tv['super_bowl'], tv['rating_household'], color='#DC267F')
plt.title('Household Rating')

# Activate the bottom subplot
plt.subplot(3, 1, 3)
plt.plot(tv['super_bowl'], tv['ad_cost'], color='#FFB000')
plt.title('Ad Cost')
plt.xlabel('SUPER BOWL')

# Improve the spacing between subplots
plt.tight_layout()
```


![png](/assets/img/superbowl-analysis_files/superbowl-analysis_11_0.png)

---
## 7. Halftime shows weren't always this great
<p>We can see viewers increased before ad costs did. Maybe the networks weren't very data savvy and were slow to react? Makes sense since DataCamp didn't exist back then.</p>
<p>Another hypothesis: maybe halftime shows weren't that good in the earlier years? The modern spectacle of the Super Bowl has a lot to do with the cultural prestige of big halftime acts. I went down a YouTube rabbit hole and it turns out the old ones weren't up to today's standards. Some offenders:</p>
<ul>
<li><a href="https://youtu.be/6wMXHxWO4ns?t=263">Super Bowl XXVI</a> in 1992: A Frosty The Snowman rap performed by children.</li>
<li><a href="https://www.youtube.com/watch?v=PKQTL1PYSag">Super Bowl XXIII</a> in 1989: An Elvis impersonator that did magic tricks and didn't even sing one Elvis song.</li>
<li><a href="https://youtu.be/oSXMNbK2e98?t=436">Super Bowl XXI</a> in 1987: Tap dancing ponies. (Okay, that's pretty awesome actually.)</li>
</ul>
<p>It turns out Michael Jackson's Super Bowl XXVII performance, one of the most watched events in American TV history, was when the NFL realized the value of Super Bowl airtime and decided they needed to sign big name acts from then on out. The halftime shows before MJ indeed weren't that impressive, which we can see by filtering our <code>halftime_musician</code> data.</p>


```python
# Display all halftime musicians for Super Bowls up to and including Super Bowl XXVII
halftime_musicians.query('super_bowl <= 27')
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
      <th>super_bowl</th>
      <th>musician</th>
      <th>num_songs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>80</th>
      <td>27</td>
      <td>Michael Jackson</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>81</th>
      <td>26</td>
      <td>Gloria Estefan</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>82</th>
      <td>26</td>
      <td>University of Minnesota Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>83</th>
      <td>25</td>
      <td>New Kids on the Block</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>84</th>
      <td>24</td>
      <td>Pete Fountain</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>85</th>
      <td>24</td>
      <td>Doug Kershaw</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>86</th>
      <td>24</td>
      <td>Irma Thomas</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>87</th>
      <td>24</td>
      <td>Pride of Nicholls Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>88</th>
      <td>24</td>
      <td>The Human Jukebox</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89</th>
      <td>24</td>
      <td>Pride of Acadiana</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>90</th>
      <td>23</td>
      <td>Elvis Presto</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>91</th>
      <td>22</td>
      <td>Chubby Checker</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>92</th>
      <td>22</td>
      <td>San Diego State University Marching Aztecs</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>93</th>
      <td>22</td>
      <td>Spirit of Troy</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>94</th>
      <td>21</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>95</th>
      <td>21</td>
      <td>Spirit of Troy</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>96</th>
      <td>20</td>
      <td>Up with People</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>97</th>
      <td>19</td>
      <td>Tops In Blue</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>98</th>
      <td>18</td>
      <td>The University of Florida Fightin' Gator March...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>99</th>
      <td>18</td>
      <td>The Florida State University Marching Chiefs</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>100</th>
      <td>17</td>
      <td>Los Angeles Unified School District All City H...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>101</th>
      <td>16</td>
      <td>Up with People</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>102</th>
      <td>15</td>
      <td>The Human Jukebox</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>103</th>
      <td>15</td>
      <td>Helen O'Connell</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>104</th>
      <td>14</td>
      <td>Up with People</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>105</th>
      <td>14</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>106</th>
      <td>13</td>
      <td>Ken Hamilton</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>107</th>
      <td>13</td>
      <td>Gramacks</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>108</th>
      <td>12</td>
      <td>Tyler Junior College Apache Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>109</th>
      <td>12</td>
      <td>Pete Fountain</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>110</th>
      <td>12</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>111</th>
      <td>11</td>
      <td>Los Angeles Unified School District All City H...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>112</th>
      <td>10</td>
      <td>Up with People</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>113</th>
      <td>9</td>
      <td>Mercer Ellington</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>114</th>
      <td>9</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>115</th>
      <td>8</td>
      <td>University of Texas Longhorn Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>116</th>
      <td>8</td>
      <td>Judy Mallett</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>117</th>
      <td>7</td>
      <td>University of Michigan Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>118</th>
      <td>7</td>
      <td>Woody Herman</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>119</th>
      <td>7</td>
      <td>Andy Williams</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>120</th>
      <td>6</td>
      <td>Ella Fitzgerald</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>121</th>
      <td>6</td>
      <td>Carol Channing</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>122</th>
      <td>6</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>123</th>
      <td>6</td>
      <td>United States Air Force Academy Cadet Chorale</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>124</th>
      <td>5</td>
      <td>Southeast Missouri State Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>125</th>
      <td>4</td>
      <td>Marguerite Piazza</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>126</th>
      <td>4</td>
      <td>Doc Severinsen</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>127</th>
      <td>4</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>128</th>
      <td>4</td>
      <td>The Human Jukebox</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>129</th>
      <td>3</td>
      <td>Florida A&amp;M University Marching 100 Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>130</th>
      <td>2</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>131</th>
      <td>1</td>
      <td>University of Arizona Symphonic Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>132</th>
      <td>1</td>
      <td>Grambling State University Tiger Marching Band</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>133</th>
      <td>1</td>
      <td>Al Hirt</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>


---
## 8. Who has the most halftime show appearances?
<p>Lots of marching bands. American jazz clarinetist Pete Fountain. Miss Texas 1973 playing a violin. Nothing against those performers, they're just simply not <a href="https://www.youtube.com/watch?v=suIg9kTGBVI">Beyoncé</a>. To be fair, no one is.</p>
<p>Let's see all of the musicians that have done at least one halftime show, including their performance counts.</p>


```python
# Count halftime show appearances for each musician and sort them from most to least
halftime_appearances = halftime_musicians.groupby('musician').count()['super_bowl'].reset_index()
halftime_appearances = halftime_appearances.sort_values('super_bowl', ascending=False)

# Display musicians with more than one halftime show appearance
halftime_appearances.query('super_bowl > 1')
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
      <th>musician</th>
      <th>super_bowl</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>28</th>
      <td>Grambling State University Tiger Marching Band</td>
      <td>6</td>
    </tr>
    <tr>
      <th>104</th>
      <td>Up with People</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Al Hirt</td>
      <td>4</td>
    </tr>
    <tr>
      <th>83</th>
      <td>The Human Jukebox</td>
      <td>3</td>
    </tr>
    <tr>
      <th>76</th>
      <td>Spirit of Troy</td>
      <td>2</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Florida A&amp;M University Marching 100 Band</td>
      <td>2</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Gloria Estefan</td>
      <td>2</td>
    </tr>
    <tr>
      <th>102</th>
      <td>University of Minnesota Marching Band</td>
      <td>2</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Bruno Mars</td>
      <td>2</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Pete Fountain</td>
      <td>2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Beyoncé</td>
      <td>2</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Justin Timberlake</td>
      <td>2</td>
    </tr>
    <tr>
      <th>57</th>
      <td>Nelly</td>
      <td>2</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Los Angeles Unified School District All City H...</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>


---
## 9. Who performed the most songs in a halftime show?
<p>The world famous <a href="https://www.youtube.com/watch?v=RL_3oqpHiDg">Grambling State University Tiger Marching Band</a> takes the crown with six appearances. Beyoncé, Justin Timberlake, Nelly, and Bruno Mars are the only post-Y2K musicians with multiple appearances (two each).</p>
<p>From our previous inspections, the <code>num_songs</code> column has lots of missing values:</p>
<ul>
<li>A lot of the marching bands don't have <code>num_songs</code> entries.</li>
<li>For non-marching bands, missing data starts occurring at Super Bowl XX.</li>
</ul>
<p>Let's filter out marching bands by filtering out musicians with the word "Marching" in them and the word "Spirit" (a common naming convention for marching bands is "Spirit of [something]"). Then we'll filter for Super Bowls after Super Bowl XX to address the missing data issue, <em>then</em> let's see who has the most number of songs.</p>


```python
# Filter out most marching bands
no_bands = halftime_musicians[~halftime_musicians.musician.str.contains('Marching')]
no_bands = no_bands[~no_bands.musician.str.contains('Spirit')]

# Plot a histogram of number of songs per performance
most_songs = int(max(no_bands['num_songs'].values))
plt.hist(no_bands.num_songs.dropna(), bins =most_songs)
plt.xlabel('Number of Songs Per Halftime Show Performance')
plt.ylabel('Number of Musicians')
plt.show()

# Sort the non-band musicians by number of songs per appearance...
no_bands = no_bands.sort_values('num_songs', ascending=False)
# ...and display the top 15
display(no_bands.head(n=15))
```


![png](/assets/img/superbowl-analysis_files/superbowl-analysis_17_0.png)



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
      <th>super_bowl</th>
      <th>musician</th>
      <th>num_songs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>52</td>
      <td>Justin Timberlake</td>
      <td>11.0</td>
    </tr>
    <tr>
      <th>70</th>
      <td>30</td>
      <td>Diana Ross</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>49</td>
      <td>Katy Perry</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>51</td>
      <td>Lady Gaga</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>90</th>
      <td>23</td>
      <td>Elvis Presto</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>33</th>
      <td>41</td>
      <td>Prince</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>47</td>
      <td>Beyoncé</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>48</td>
      <td>Bruno Mars</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>50</td>
      <td>Coldplay</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>45</td>
      <td>The Black Eyed Peas</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>46</td>
      <td>Madonna</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>44</td>
      <td>The Who</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>80</th>
      <td>27</td>
      <td>Michael Jackson</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>64</th>
      <td>32</td>
      <td>The Temptations</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>39</td>
      <td>Paul McCartney</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>

---
## 10. Conclusion
<p>So most non-band musicians do 1-3 songs per halftime show. It's important to note that the duration of the halftime show is fixed (roughly 12 minutes) so songs per performance is more a measure of how many hit songs you have. JT went off in 2018, wow. 11 songs! Diana Ross comes in second with 10 in her medley in 1996.</p>
<p>In this notebook, we loaded, cleaned, then explored Super Bowl game, television, and halftime show data. We visualized the distributions of combined points, point differences, and halftime show performances using histograms. We used line plots to see how ad cost increases lagged behind viewership increases. And we discovered that blowouts do appear to lead to a drop in viewers.</p>
<p>This year's Big Game will be here before you know it. Who do you think will win Super Bowl LIII?</p>


```python
# 2018-2019 conference champions
patriots = 'New England Patriots'
rams = 'Los Angeles Rams'

# Who will win Super Bowl LII?
super_bowl_LII_winner = patriots
print('The winner of Super Bowl LII will be the', super_bowl_LII_winner)
```

    The winner of Super Bowl LIII will be the New England Patriots



---
## Dig deeper?

You can find out more about this project at [Github](https://github.com/Kau5h1K/superbowl-analysis).
{: .notice}
