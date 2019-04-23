---
layout: post
title: "Data Analysis in Baseball using MLB's Statcast data"
tags: [ Project, python, Data Visualization, Statcast, Aaron Judge,Giancarlo Stanton]
date: 2019-03-18
excerpt: "There's a new era of data analysis in baseball. Using a new technology called Statcast, Major League Baseball is now collecting the precise location and movements of its baseballs and players. <br />
In this project, I used Statcast data to compare the home runs of two of baseball's brightest (and largest) stars, Aaron Judge (6'7\") and Giancarlo Stanton (6'6\"), both of whom now play for the New York Yankees."
comments: True
project: True
---

---
## Data
This project uses a dataset which is available [here](https://github.com/Kau5h1K/baseball-analysis/tree/master/datasets).The dataset used in this project is from [Baseball Savant](https://baseballsavant.mlb.com/about).

---
## 1. The Statcast revolution
<p><img style="float: left;margin:5px 20px 5px 1px" src="https://s3.amazonaws.com/assets.datacamp.com/production/project_250/img/judge_wide.jpg"></p>
<p>This is Aaron Judge. Judge is one of the physically largest players in Major League Baseball standing 6 feet 7 inches (2.01 m) tall and weighing 282 pounds (128 kg). He also hit the <a href="https://www.mlb.com/news/aaron-judge-sets-statcast-exit-velocity-record/c-235640846">hardest home run</a> ever recorded. How do we know this? <strong>Statcast</strong>.</p>
<p>Statcast is a state-of-the-art tracking system that uses high-resolution cameras and radar equipment to measure the precise location and movement of baseballs and baseball players. Introduced in 2015 to all 30 major league ballparks, Statcast data is revolutionizing the game. Teams are engaging in an "arms race" of data analysis, hiring analysts left and right in an attempt to gain an edge over their competition. This <a href="https://www.youtube.com/watch?v=9rOKGKhQe8U">video</a> describing the system is incredible.</p>
<p><strong>In this notebook</strong>, we're going to wrangle, analyze, and visualize Statcast data to compare Mr. Judge and another (extremely large) teammate of his. Let's start by loading the data into our Notebook. There are two CSV files, <code>judge.csv</code> and <code>stanton.csv</code>, both of which contain Statcast data for 2015-2017. We'll use pandas DataFrames to store this data. Let's also load our data visualization libraries, matplotlib and seaborn.</p>


```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline

# Load Aaron Judge's Statcast data
judge = pd.read_csv('datasets/judge.csv')

# Load Giancarlo Stanton's Statcast data
stanton = pd.read_csv('datasets/stanton.csv')
```
---
## 2. What can Statcast measure?
<p>The better question might be, what can't Statcast measure?</p>
<blockquote>
  <p>Starting with the pitcher, Statcast can measure simple data points such as velocity. At the same time, Statcast digs a whole lot deeper, also measuring the release point and spin rate of every pitch.</p>
  <p>Moving on to hitters, Statcast is capable of measuring the exit velocity, launch angle and vector of the ball as it comes off the bat. From there, Statcast can also track the hang time and projected distance that a ball travels.</p>
</blockquote>
<p>Let's inspect the last five rows of the <code>judge</code> DataFrame. You'll see that each row represents one pitch thrown to a batter. You'll also see that some columns have esoteric names. If these don't make sense now, don't worry. The relevant ones will be explained as necessary.</p>


```python
# Display all columns (pandas will collapse some columns if we don't set this option)
pd.set_option('display.max_columns', None)

# Display the last five rows of the Aaron Judge file
judge.tail()
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
      <th>pitch_type</th>
      <th>game_date</th>
      <th>release_speed</th>
      <th>release_pos_x</th>
      <th>release_pos_z</th>
      <th>player_name</th>
      <th>batter</th>
      <th>pitcher</th>
      <th>events</th>
      <th>description</th>
      <th>spin_dir</th>
      <th>spin_rate_deprecated</th>
      <th>break_angle_deprecated</th>
      <th>break_length_deprecated</th>
      <th>zone</th>
      <th>des</th>
      <th>game_type</th>
      <th>stand</th>
      <th>p_throws</th>
      <th>home_team</th>
      <th>away_team</th>
      <th>type</th>
      <th>hit_location</th>
      <th>bb_type</th>
      <th>balls</th>
      <th>strikes</th>
      <th>game_year</th>
      <th>pfx_x</th>
      <th>pfx_z</th>
      <th>plate_x</th>
      <th>plate_z</th>
      <th>on_3b</th>
      <th>on_2b</th>
      <th>on_1b</th>
      <th>outs_when_up</th>
      <th>inning</th>
      <th>inning_topbot</th>
      <th>hc_x</th>
      <th>hc_y</th>
      <th>tfs_deprecated</th>
      <th>tfs_zulu_deprecated</th>
      <th>pos2_person_id</th>
      <th>umpire</th>
      <th>sv_id</th>
      <th>vx0</th>
      <th>vy0</th>
      <th>vz0</th>
      <th>ax</th>
      <th>ay</th>
      <th>az</th>
      <th>sz_top</th>
      <th>sz_bot</th>
      <th>hit_distance_sc</th>
      <th>launch_speed</th>
      <th>launch_angle</th>
      <th>effective_speed</th>
      <th>release_spin_rate</th>
      <th>release_extension</th>
      <th>game_pk</th>
      <th>pos1_person_id</th>
      <th>pos2_person_id.1</th>
      <th>pos3_person_id</th>
      <th>pos4_person_id</th>
      <th>pos5_person_id</th>
      <th>pos6_person_id</th>
      <th>pos7_person_id</th>
      <th>pos8_person_id</th>
      <th>pos9_person_id</th>
      <th>release_pos_y</th>
      <th>estimated_ba_using_speedangle</th>
      <th>estimated_woba_using_speedangle</th>
      <th>woba_value</th>
      <th>woba_denom</th>
      <th>babip_value</th>
      <th>iso_value</th>
      <th>launch_speed_angle</th>
      <th>at_bat_number</th>
      <th>pitch_number</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3431</th>
      <td>CH</td>
      <td>2016-08-13</td>
      <td>85.6</td>
      <td>-1.9659</td>
      <td>5.9113</td>
      <td>Aaron Judge</td>
      <td>592450</td>
      <td>542882</td>
      <td>NaN</td>
      <td>ball</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14.0</td>
      <td>NaN</td>
      <td>R</td>
      <td>R</td>
      <td>R</td>
      <td>NYY</td>
      <td>TB</td>
      <td>B</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>2016</td>
      <td>-0.379108</td>
      <td>0.370567</td>
      <td>0.739</td>
      <td>1.442</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>5</td>
      <td>Bot</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>571912.0</td>
      <td>NaN</td>
      <td>160813_144259</td>
      <td>6.960</td>
      <td>-124.371</td>
      <td>-4.756</td>
      <td>-2.821</td>
      <td>23.634</td>
      <td>-30.220</td>
      <td>3.93</td>
      <td>1.82</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>84.459</td>
      <td>1552.0</td>
      <td>5.683</td>
      <td>448611</td>
      <td>542882.0</td>
      <td>571912.0</td>
      <td>543543.0</td>
      <td>523253.0</td>
      <td>446334.0</td>
      <td>622110.0</td>
      <td>545338.0</td>
      <td>595281.0</td>
      <td>543484.0</td>
      <td>54.8144</td>
      <td>0.00</td>
      <td>0.000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>36</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3432</th>
      <td>CH</td>
      <td>2016-08-13</td>
      <td>87.6</td>
      <td>-1.9318</td>
      <td>5.9349</td>
      <td>Aaron Judge</td>
      <td>592450</td>
      <td>542882</td>
      <td>home_run</td>
      <td>hit_into_play_score</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4.0</td>
      <td>Aaron Judge homers (1) on a fly ball to center...</td>
      <td>R</td>
      <td>R</td>
      <td>R</td>
      <td>NYY</td>
      <td>TB</td>
      <td>X</td>
      <td>NaN</td>
      <td>fly_ball</td>
      <td>1</td>
      <td>2</td>
      <td>2016</td>
      <td>-0.295608</td>
      <td>0.320400</td>
      <td>-0.419</td>
      <td>3.273</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>2</td>
      <td>Bot</td>
      <td>130.45</td>
      <td>14.58</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>571912.0</td>
      <td>NaN</td>
      <td>160813_135833</td>
      <td>4.287</td>
      <td>-127.452</td>
      <td>-0.882</td>
      <td>-1.972</td>
      <td>24.694</td>
      <td>-30.705</td>
      <td>4.01</td>
      <td>1.82</td>
      <td>446.0</td>
      <td>108.8</td>
      <td>27.410</td>
      <td>86.412</td>
      <td>1947.0</td>
      <td>5.691</td>
      <td>448611</td>
      <td>542882.0</td>
      <td>571912.0</td>
      <td>543543.0</td>
      <td>523253.0</td>
      <td>446334.0</td>
      <td>622110.0</td>
      <td>545338.0</td>
      <td>595281.0</td>
      <td>543484.0</td>
      <td>54.8064</td>
      <td>0.98</td>
      <td>1.937</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>6.0</td>
      <td>14</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3433</th>
      <td>CH</td>
      <td>2016-08-13</td>
      <td>87.2</td>
      <td>-2.0285</td>
      <td>5.8656</td>
      <td>Aaron Judge</td>
      <td>592450</td>
      <td>542882</td>
      <td>NaN</td>
      <td>ball</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14.0</td>
      <td>NaN</td>
      <td>R</td>
      <td>R</td>
      <td>R</td>
      <td>NYY</td>
      <td>TB</td>
      <td>B</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>2</td>
      <td>2016</td>
      <td>-0.668575</td>
      <td>0.198567</td>
      <td>0.561</td>
      <td>0.960</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>2</td>
      <td>Bot</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>571912.0</td>
      <td>NaN</td>
      <td>160813_135815</td>
      <td>7.491</td>
      <td>-126.665</td>
      <td>-5.862</td>
      <td>-6.393</td>
      <td>21.952</td>
      <td>-32.121</td>
      <td>4.01</td>
      <td>1.82</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>86.368</td>
      <td>1761.0</td>
      <td>5.721</td>
      <td>448611</td>
      <td>542882.0</td>
      <td>571912.0</td>
      <td>543543.0</td>
      <td>523253.0</td>
      <td>446334.0</td>
      <td>622110.0</td>
      <td>545338.0</td>
      <td>595281.0</td>
      <td>543484.0</td>
      <td>54.7770</td>
      <td>0.00</td>
      <td>0.000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3434</th>
      <td>CU</td>
      <td>2016-08-13</td>
      <td>79.7</td>
      <td>-1.7108</td>
      <td>6.1926</td>
      <td>Aaron Judge</td>
      <td>592450</td>
      <td>542882</td>
      <td>NaN</td>
      <td>foul</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4.0</td>
      <td>NaN</td>
      <td>R</td>
      <td>R</td>
      <td>R</td>
      <td>NYY</td>
      <td>TB</td>
      <td>S</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>1</td>
      <td>2016</td>
      <td>0.397442</td>
      <td>-0.614133</td>
      <td>-0.803</td>
      <td>2.742</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>2</td>
      <td>Bot</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>571912.0</td>
      <td>NaN</td>
      <td>160813_135752</td>
      <td>1.254</td>
      <td>-116.062</td>
      <td>0.439</td>
      <td>5.184</td>
      <td>21.328</td>
      <td>-39.866</td>
      <td>4.01</td>
      <td>1.82</td>
      <td>9.0</td>
      <td>55.8</td>
      <td>-24.973</td>
      <td>77.723</td>
      <td>2640.0</td>
      <td>5.022</td>
      <td>448611</td>
      <td>542882.0</td>
      <td>571912.0</td>
      <td>543543.0</td>
      <td>523253.0</td>
      <td>446334.0</td>
      <td>622110.0</td>
      <td>545338.0</td>
      <td>595281.0</td>
      <td>543484.0</td>
      <td>55.4756</td>
      <td>0.00</td>
      <td>0.000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>14</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3435</th>
      <td>FF</td>
      <td>2016-08-13</td>
      <td>93.2</td>
      <td>-1.8476</td>
      <td>6.0063</td>
      <td>Aaron Judge</td>
      <td>592450</td>
      <td>542882</td>
      <td>NaN</td>
      <td>called_strike</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.0</td>
      <td>NaN</td>
      <td>R</td>
      <td>R</td>
      <td>R</td>
      <td>NYY</td>
      <td>TB</td>
      <td>S</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>2016</td>
      <td>-0.823050</td>
      <td>1.623300</td>
      <td>-0.273</td>
      <td>2.471</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>2</td>
      <td>Bot</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>571912.0</td>
      <td>NaN</td>
      <td>160813_135736</td>
      <td>5.994</td>
      <td>-135.497</td>
      <td>-6.736</td>
      <td>-9.360</td>
      <td>26.782</td>
      <td>-13.446</td>
      <td>4.01</td>
      <td>1.82</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>92.696</td>
      <td>2271.0</td>
      <td>6.068</td>
      <td>448611</td>
      <td>542882.0</td>
      <td>571912.0</td>
      <td>543543.0</td>
      <td>523253.0</td>
      <td>446334.0</td>
      <td>622110.0</td>
      <td>545338.0</td>
      <td>595281.0</td>
      <td>543484.0</td>
      <td>54.4299</td>
      <td>0.00</td>
      <td>0.000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>


---
## 3. Aaron Judge and Giancarlo Stanton, prolific sluggers
<p><img style="float: left;margin:5px 20px 5px 1px" src="https://s3.amazonaws.com/assets.datacamp.com/production/project_250/img/stanton_wide.jpg"></p>
<p>This is Giancarlo Stanton. He is also a very large human being, standing 6 feet 6 inches tall and weighing 245 pounds. Despite not wearing the same jersey as Judge in the pictures provided, in 2018 they will be teammates on the New York Yankees. They are similar in a lot of ways, one being that they hit a lot of home runs. Stanton and Judge led baseball in home runs in 2017, with <a href="https://www.youtube.com/watch?v=tJ6Bz5P6dg4">59</a> and <a href="https://www.youtube.com/watch?v=Gw3pFHMM9fk">52</a>, respectively. These are exceptional totals - the player in third "only" had 45 home runs.</p>
<p>Stanton and Judge are also different in many ways. One is <a href="http://m.mlb.com/glossary/statcast/batted-ball-event">batted ball events</a>, which is any batted ball that produces a result. This includes outs, hits, and errors. Next, you'll find the counts of batted ball events for each player in 2017. The frequencies of other events are quite different.</p>


```python
# All of Aaron Judge's batted ball events in 2017
judge_events_2017 = judge.query('game_year == 2017').events
print("Aaron Judge batted ball event totals, 2017:")
print(judge_events_2017.value_counts())

# All of Giancarlo Stanton's batted ball events in 2017
stanton_events_2017 = stanton.query('game_year == 2017').events
print("\nGiancarlo Stanton batted ball event totals, 2017:")
print(stanton_events_2017.value_counts())
```

    Aaron Judge batted ball event totals, 2017:
    strikeout                    207
    field_out                    146
    walk                         116
    single                        75
    home_run                      52
    double                        24
    grounded_into_double_play     15
    intent_walk                   11
    force_out                     11
    hit_by_pitch                   5
    sac_fly                        4
    field_error                    4
    fielders_choice_out            4
    triple                         3
    strikeout_double_play          1
    Name: events, dtype: int64

    Giancarlo Stanton batted ball event totals, 2017:
    field_out                    239
    strikeout                    161
    single                        77
    walk                          72
    home_run                      59
    double                        32
    intent_walk                   13
    grounded_into_double_play     13
    force_out                      7
    hit_by_pitch                   7
    field_error                    5
    sac_fly                        3
    fielders_choice_out            2
    strikeout_double_play          2
    pickoff_1b                     1
    Name: events, dtype: int64

---
## 4. Analyzing home runs with Statcast data
<p>So Judge walks and strikes out more than Stanton. Stanton flies out more than Judge. But let's get into their hitting profiles in more detail. Two of the most groundbreaking Statcast metrics are launch angle and exit velocity:</p>
<ul>
<li><a href="http://m.mlb.com/glossary/statcast/launch-angle">Launch angle</a>: the vertical angle at which the ball leaves a player's bat</li>
<li><a href="http://m.mlb.com/glossary/statcast/exit-velocity">Exit velocity</a>: the speed of the baseball as it comes off the bat</li>
</ul>
<p>This new data has changed the way teams value both hitters and pitchers. Why? As per the <a href="https://www.washingtonpost.com/graphics/sports/mlb-launch-angles-story/?utm_term=.8d088d31f098">Washington Post</a>:</p>
<blockquote>
  <p>Balls hit with a high launch angle are more likely to result in a hit. Hit fast enough and at the right angle, they become home runs.</p>
</blockquote>
<p>Let's look at exit velocity vs. launch angle and let's focus on home runs only (2015-2017). The first two plots show data points. The second two show smoothed contours to represent density.</p>


```python
# Filter to include home runs only
judge_hr = judge.query('events == "home_run"')
stanton_hr = stanton.query('events == "home_run"')

# Create a figure with two scatter plots of launch speed vs. launch angle, one for each player's home runs
fig1, axs1 = plt.subplots(ncols=2, sharex=True, sharey=True)
sns.regplot(x='launch_speed', y='launch_angle', fit_reg=False, color='tab:blue', data=judge_hr, ax=axs1[0]).set_title('Aaron Judge\nHome Runs, 2015-2017')
sns.regplot(x='launch_speed', y='launch_angle', fit_reg=False, color='tab:blue', data=stanton_hr, ax=axs1[1]).set_title('Giancarlo Stanton\nHome Runs, 2015-2017')

# Create a figure with two KDE plots of launch speed vs. launch angle, one for each player's home runs
fig2, axs2 = plt.subplots(ncols=2, sharex=True, sharey=True)
sns.kdeplot(judge_hr.launch_speed, judge_hr.launch_angle, cmap="Blues", shade=True, shade_lowest=False, ax=axs2[0]).set_title('Aaron Judge\nHome Runs, 2015-2017')
sns.kdeplot(stanton_hr.launch_speed, stanton_hr.launch_angle, cmap="Blues", shade=True, shade_lowest=False, ax=axs2[1]).set_title('Giancarlo Stanton\nHome Runs, 2015-2017')
```




    Text(0.5, 1.0, 'Giancarlo Stanton\nHome Runs, 2015-2017')




![png](/assets/img/baseball-analysis_files/baseball-analysis_7_1.png)



![png](/assets/img/baseball-analysis_files/baseball-analysis_7_2.png)

---
## 5. Home runs by pitch velocity
<p>It appears that Stanton hits his home runs slightly lower and slightly harder than Judge, though this needs to be taken with a grain of salt given the small sample size of home runs.</p>
<p>Not only does Statcast measure the velocity of the ball coming off of the bat, it measures the velocity of the ball coming out of the pitcher's hand and begins its journey towards the plate. We can use this data to compare Stanton and Judge's home runs in terms of pitch velocity. Next you'll find box plots displaying the five-number summaries for each player: minimum, first quartile, median, third quartile, and maximum.</p>


```python
# Combine the Judge and Stanton home run DataFrames for easy boxplot plotting
judge_stanton_hr = pd.concat([judge_hr,stanton_hr])

# Create a boxplot that describes the pitch velocity of each player's home runs
sns.boxplot(x=judge_stanton_hr.player_name,y=judge_stanton_hr.release_speed,color='tab:blue').set_title('Home Runs, 2015-2017')
```




    Text(0.5, 1.0, 'Home Runs, 2015-2017')




![png](/assets/img/baseball-analysis_files/baseball-analysis_9_1.png)

---
## 6. Home runs by pitch location (I)
<p>So Judge appears to hit his home runs off of faster pitches than Stanton. We might call Judge a fastball hitter. Stanton appears agnostic to pitch speed and likely pitch movement since slower pitches (e.g. curveballs, sliders, and changeups) tend to have more break. Statcast <em>does</em> track pitch movement and type but let's move on to something else: <strong>pitch location</strong>. Statcast tracks the zone the pitch is in when it crosses the plate. The zone numbering looks like this (from the catcher's point of view):</p>
<p><img style="margin:5px 20px 5px 1px; width:20%;" src="https://s3.amazonaws.com/assets.datacamp.com/production/project_250/img/zone.png"></p>
<p>We can plot this using a 2D histogram. For simplicity, let's only look at strikes, which gives us a 9x9 grid. We can view each zone as coordinates on a 2D plot, the bottom left corner being (1,1) and the top right corner being (3,3). Let's set up a function to assign x-coordinates to each pitch.</p>


```python
def assign_x_coord(row):
    """
    Assigns an x-coordinate to Statcast's strike zone numbers. Zones 11, 12, 13,
    and 14 are ignored for plotting simplicity.
    """
    # Left third of strike zone
    if row.zone in [1, 4, 7]:
        return 1
    # Middle third of strike zone
    if row.zone in [2, 5, 8]:
        return 2
    # Right third of strike zone
    if row.zone in [3, 6, 9]:
        return 3
```
---
## 7. Home runs by pitch location (II)
<p>And let's do the same but for y-coordinates.</p>


```python
def assign_y_coord(row):
    """
    Assigns a y-coordinate to Statcast's strike zone numbers. Zones 11, 12, 13,
    and 14 are ignored for plotting simplicity.
    """
    # Upper third of strike zone
    if row.zone in [1,2,3]:
        return 3
    # Middle third of strike zone
    if row.zone in [4,5,6]:
        return 2
    # Lower third of strike zone
    if row.zone in [7,8,9]:
        return 1
```
---
## 8. Aaron Judge's home run zone
<p>Now we can apply the functions we've created then construct our 2D histograms. First, for Aaron Judge (again, for pitches in the strike zone that resulted in home runs).</p>


```python
# Zones 11, 12, 13, and 14 are to be ignored for plotting simplicity
judge_strike_hr = judge_hr.copy().loc[judge_hr.zone <= 9]

# Assign Cartesian coordinates to pitches in the strike zone for Judge home runs
judge_strike_hr['zone_x'] = judge_strike_hr.apply(assign_x_coord,axis=1)
judge_strike_hr['zone_y'] = judge_strike_hr.apply(assign_y_coord,axis=1)

# Plot Judge's home run zone as a 2D histogram with a colorbar
plt.hist2d(judge_strike_hr['zone_x'], judge_strike_hr['zone_y'], bins = 3, cmap='Blues')
plt.title('Aaron Judge Home Runs on\n Pitches in the Strike Zone, 2015-2017')
plt.gca().get_xaxis().set_visible(False)
plt.gca().get_yaxis().set_visible(False)
cb = plt.colorbar()
cb.set_label('Counts in Bin')
```


![png](/assets/img/baseball-analysis_files/baseball-analysis_15_0.png)

---
## 9. Giancarlo Stanton's home run zone
<p>And now for Giancarlo Stanton.</p>


```python
# Zones 11, 12, 13, and 14 are to be ignored for plotting simplicity
stanton_strike_hr = stanton_hr.copy().loc[stanton_hr.zone <= 9]

# Assign Cartesian coordinates to pitches in the strike zone for Stanton home runs
stanton_strike_hr['zone_x'] = stanton_strike_hr.apply(assign_x_coord,axis=1)
stanton_strike_hr['zone_y'] = stanton_strike_hr.apply(assign_y_coord,axis=1)

# Plot Stanton's home run zone as a 2D histogram with a colorbar
plt.hist2d(stanton_strike_hr['zone_x'],stanton_strike_hr['zone_y'],bins=3,cmap='Blues')
plt.title('Giancarlo Stanton Home Runs on\n Pitches in the Strike Zone, 2015-2017')
plt.gca().get_xaxis().set_visible(False)
plt.gca().get_yaxis().set_visible(False)
cb = plt.colorbar()
cb.set_label('Counts in Bin')
```


![png](/assets/img/baseball-analysis_files/baseball-analysis_17_0.png)

---
## 10. Should opposing pitchers be scared?
<p>A few takeaways:</p>
<ul>
<li>Stanton does not hit many home runs on pitches in the upper third of the strike zone.</li>
<li>Like pretty much every hitter ever, both players love pitches in the horizontal and vertical middle of the plate.</li>
<li>Judge's least favorite home run pitch appears to be high-away while Stanton's appears to be low-away.</li>
<li>If we were to describe Stanton's home run zone, it'd be middle-inside. Judge's home run zone is much more spread out.</li>
</ul>
<p>The grand takeaway from this whole exercise: Aaron Judge and Giancarlo Stanton are not identical despite their superficial similarities. In terms of home runs, their launch profiles, as well as their pitch speed and location preferences, are different.</p>
<p>Should opposing pitchers still be scared?</p>


```python
# Should opposing pitchers be wary of Aaron Judge and Giancarlo Stanton
should_pitchers_be_scared = True
```

---
## Further Reading
If you'd like more Statcast content to digest, this video [(A culmination of special Statcast records in 2017 from MLB's YouTube channel)](https://www.youtube.com/watch?v=tzPKlQXo6hk) and this article [(Major League Baseball's Statcast Can Break Sabermetrics by Emma Baccellieri)](https://deadspin.com/major-league-baseballs-statcast-can-break-sabermetrics-1820987737) are excellent.

---
## Dig deeper?

You can find out more about this project at [Github](https://github.com/Kau5h1K/baseball-analysis).
{: .notice}
