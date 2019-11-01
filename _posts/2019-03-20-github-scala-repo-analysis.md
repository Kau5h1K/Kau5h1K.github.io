---
layout: post
title: "Analyzing the GitHub History of the Scala Language"
tags: [ Project, python, pandas, Cleaning Data, Merging and Manipulating dataframes, timeseries, datetime, pivot]
date: 2019-03-20
excerpt: "Open source projects contain entire development histories - who made changes, the changes themselves, and code reviews.<br/>
In this project, I aim to read in, clean up, and visualize the real-world project repository of Scala that spans data from a version control system (Git) as well as a project hosting site (GitHub). With almost 30,000 commits and a history spanning over ten years, Scala is a mature language. I plan to find out who has had the most influence on its development and who are the experts."
comments: True
project: True
badge: Available
badge-color: "green"
---

---
## Data
This project uses a dataset which is available [here](https://github.com/Kau5h1K/github-scala-repo-analysis/tree/master/datasets). The dataset includes the project history of [Scala](http://www.scala-lang.org/) retrieved from Git and GitHub as a set of CSV files.

---
## 1. Scala's real-world project repository data
<p>With almost 30k commits and a history spanning over ten years, Scala is a mature programming language. It is a general-purpose programming language that has recently become another prominent language for data scientists.</p>
<p>Scala is also an open source project. Open source projects have the advantage that their entire development histories -- who made changes, what was changed, code reviews, etc. -- publicly available. </p>
<p>I'm going to read in, clean up, and visualize the real world project repository of Scala that spans data from a version control system (Git) as well as a project hosting site (GitHub). I will find out who has had the most influence on its development and who are the experts.</p>
<p>The dataset I will use, which has been previously mined and extracted directly from GitHub, is comprised of two files:</p>
<ol>
<li><code>pulls.csv</code> contains the basic information about the pull requests.</li>
<li><code>pull_files.csv</code> contains the files that were modified by each pull request.</li>
</ol>


```python
# Importing pandas
import pandas as pd
# Loading in the data
pulls = pd.read_csv('datasets/pulls.csv')
pull_files = pd.read_csv('datasets/pull_files.csv')
```

---
## 2. Cleaning the data
<p>The raw data extracted from GitHub contains dates in the ISO8601 format. However, <code>pandas</code> imports them as regular strings. To make our analysis easier, we need to convert the strings into Python's <code>DateTime</code> objects. <code>DateTime</code> objects have the important property that they can be compared and sorted.</p>
<p>The pull request times are all in UTC (also known as Coordinated Universal Time). The commit times, however, are in the local time of the author with time zone information (number of hours difference from UTC). To make comparisons easy, we should convert all times to UTC.</p>


```python
# Convert the date for the pulls object
pulls['date'] = pd.to_datetime(pulls['date'],utc = True)
pulls
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
      <th>pid</th>
      <th>user</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>163061502</td>
      <td>joroKr21</td>
      <td>2018-01-15 23:44:52+00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>163057333</td>
      <td>mkeskells</td>
      <td>2018-01-15 23:05:06+00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>162985594</td>
      <td>lrytz</td>
      <td>2018-01-15 15:52:39+00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>162838837</td>
      <td>zuvizudar</td>
      <td>2018-01-14 19:16:16+00:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>162765232</td>
      <td>howtonotwin</td>
      <td>2018-01-13 04:37:17+00:00</td>
    </tr>
    <tr>
      <th>6</th>
      <td>162643301</td>
      <td>hrhino</td>
      <td>2018-01-12 14:29:49+00:00</td>
    </tr>
    <tr>
      <th>7</th>
      <td>162256029</td>
      <td>mkeskells</td>
      <td>2018-01-10 23:20:03+00:00</td>
    </tr>
    <tr>
      <th>8</th>
      <td>162197834</td>
      <td>SethTisue</td>
      <td>2018-01-10 18:31:25+00:00</td>
    </tr>
    <tr>
      <th>9</th>
      <td>162011899</td>
      <td>joroKr21</td>
      <td>2018-01-10 00:45:57+00:00</td>
    </tr>
    <tr>
      <th>10</th>
      <td>162009313</td>
      <td>SethTisue</td>
      <td>2018-01-10 00:23:34+00:00</td>
    </tr>
    <tr>
      <th>11</th>
      <td>162003773</td>
      <td>SethTisue</td>
      <td>2018-01-09 23:40:55+00:00</td>
    </tr>
    <tr>
      <th>12</th>
      <td>161996531</td>
      <td>hrhino</td>
      <td>2018-01-09 22:54:03+00:00</td>
    </tr>
    <tr>
      <th>13</th>
      <td>161993479</td>
      <td>SethTisue</td>
      <td>2018-01-09 22:36:47+00:00</td>
    </tr>
    <tr>
      <th>14</th>
      <td>161752108</td>
      <td>hrhino</td>
      <td>2018-01-08 23:32:26+00:00</td>
    </tr>
    <tr>
      <th>15</th>
      <td>161750322</td>
      <td>som-snytt</td>
      <td>2018-01-08 23:20:00+00:00</td>
    </tr>
    <tr>
      <th>16</th>
      <td>161545073</td>
      <td>svatsan</td>
      <td>2018-01-07 23:45:21+00:00</td>
    </tr>
    <tr>
      <th>17</th>
      <td>161491269</td>
      <td>howtonotwin</td>
      <td>2018-01-06 18:52:25+00:00</td>
    </tr>
    <tr>
      <th>18</th>
      <td>161406633</td>
      <td>ashawley</td>
      <td>2018-01-05 19:09:13+00:00</td>
    </tr>
    <tr>
      <th>19</th>
      <td>161236235</td>
      <td>som-snytt</td>
      <td>2018-01-04 22:56:41+00:00</td>
    </tr>
    <tr>
      <th>20</th>
      <td>161046157</td>
      <td>howtonotwin</td>
      <td>2018-01-04 03:28:54+00:00</td>
    </tr>
    <tr>
      <th>21</th>
      <td>160969081</td>
      <td>naferx</td>
      <td>2018-01-03 18:13:06+00:00</td>
    </tr>
    <tr>
      <th>22</th>
      <td>160659200</td>
      <td>rcoh</td>
      <td>2018-01-01 20:18:47+00:00</td>
    </tr>
    <tr>
      <th>23</th>
      <td>160628967</td>
      <td>som-snytt</td>
      <td>2017-12-31 19:46:37+00:00</td>
    </tr>
    <tr>
      <th>24</th>
      <td>160607241</td>
      <td>hrhino</td>
      <td>2017-12-31 02:03:53+00:00</td>
    </tr>
    <tr>
      <th>25</th>
      <td>160486436</td>
      <td>som-snytt</td>
      <td>2017-12-29 08:19:09+00:00</td>
    </tr>
    <tr>
      <th>26</th>
      <td>160465311</td>
      <td>som-snytt</td>
      <td>2017-12-29 02:24:06+00:00</td>
    </tr>
    <tr>
      <th>27</th>
      <td>160325010</td>
      <td>ashawley</td>
      <td>2017-12-28 02:24:52+00:00</td>
    </tr>
    <tr>
      <th>28</th>
      <td>159955300</td>
      <td>som-snytt</td>
      <td>2017-12-23 03:38:45+00:00</td>
    </tr>
    <tr>
      <th>29</th>
      <td>159882248</td>
      <td>jbrower2</td>
      <td>2017-12-22 14:57:13+00:00</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6170</th>
      <td>559172</td>
      <td>joshmarcus</td>
      <td>2011-12-05 17:21:01+00:00</td>
    </tr>
    <tr>
      <th>6171</th>
      <td>558806</td>
      <td>xeno-by</td>
      <td>2011-12-05 16:01:55+00:00</td>
    </tr>
    <tr>
      <th>6172</th>
      <td>558095</td>
      <td>xeno-by</td>
      <td>2011-12-05 12:22:57+00:00</td>
    </tr>
    <tr>
      <th>6173</th>
      <td>557970</td>
      <td>szeiger</td>
      <td>2011-12-05 11:37:07+00:00</td>
    </tr>
    <tr>
      <th>6174</th>
      <td>557967</td>
      <td>szeiger</td>
      <td>2011-12-05 11:36:27+00:00</td>
    </tr>
    <tr>
      <th>6175</th>
      <td>556811</td>
      <td>xeno-by</td>
      <td>2011-12-04 23:06:49+00:00</td>
    </tr>
    <tr>
      <th>6176</th>
      <td>556556</td>
      <td>xeno-by</td>
      <td>2011-12-04 20:15:49+00:00</td>
    </tr>
    <tr>
      <th>6177</th>
      <td>554542</td>
      <td>ijuma</td>
      <td>2011-12-03 02:19:13+00:00</td>
    </tr>
    <tr>
      <th>6178</th>
      <td>553456</td>
      <td>dcsobral</td>
      <td>2011-12-02 20:48:20+00:00</td>
    </tr>
    <tr>
      <th>6179</th>
      <td>553265</td>
      <td>dcsobral</td>
      <td>2011-12-02 19:59:57+00:00</td>
    </tr>
    <tr>
      <th>6180</th>
      <td>553095</td>
      <td>dcsobral</td>
      <td>2011-12-02 19:17:44+00:00</td>
    </tr>
    <tr>
      <th>6181</th>
      <td>552535</td>
      <td>soc</td>
      <td>2011-12-02 17:04:45+00:00</td>
    </tr>
    <tr>
      <th>6182</th>
      <td>552482</td>
      <td>soc</td>
      <td>2011-12-02 16:52:08+00:00</td>
    </tr>
    <tr>
      <th>6183</th>
      <td>552481</td>
      <td>soc</td>
      <td>2011-12-02 16:51:36+00:00</td>
    </tr>
    <tr>
      <th>6184</th>
      <td>551968</td>
      <td>soc</td>
      <td>2011-12-02 14:46:17+00:00</td>
    </tr>
    <tr>
      <th>6185</th>
      <td>551904</td>
      <td>jsuereth</td>
      <td>2011-12-02 14:27:41+00:00</td>
    </tr>
    <tr>
      <th>6186</th>
      <td>551651</td>
      <td>szeiger</td>
      <td>2011-12-02 13:02:29+00:00</td>
    </tr>
    <tr>
      <th>6187</th>
      <td>551505</td>
      <td>szeiger</td>
      <td>2011-12-02 12:04:52+00:00</td>
    </tr>
    <tr>
      <th>6188</th>
      <td>551474</td>
      <td>szeiger</td>
      <td>2011-12-02 11:46:43+00:00</td>
    </tr>
    <tr>
      <th>6189</th>
      <td>551375</td>
      <td>soc</td>
      <td>2011-12-02 11:02:49+00:00</td>
    </tr>
    <tr>
      <th>6190</th>
      <td>550508</td>
      <td>soc</td>
      <td>2011-12-02 02:53:07+00:00</td>
    </tr>
    <tr>
      <th>6191</th>
      <td>548900</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:46:20+00:00</td>
    </tr>
    <tr>
      <th>6192</th>
      <td>548838</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:35:53+00:00</td>
    </tr>
    <tr>
      <th>6193</th>
      <td>548744</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:15:58+00:00</td>
    </tr>
    <tr>
      <th>6194</th>
      <td>548694</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:00:36+00:00</td>
    </tr>
    <tr>
      <th>6195</th>
      <td>547941</td>
      <td>xeno-by</td>
      <td>2011-12-01 16:16:42+00:00</td>
    </tr>
    <tr>
      <th>6196</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
    </tr>
    <tr>
      <th>6197</th>
      <td>547150</td>
      <td>odersky</td>
      <td>2011-12-01 12:39:40+00:00</td>
    </tr>
    <tr>
      <th>6198</th>
      <td>547003</td>
      <td>robinst</td>
      <td>2011-12-01 11:42:26+00:00</td>
    </tr>
    <tr>
      <th>6199</th>
      <td>546847</td>
      <td>odersky</td>
      <td>2011-12-01 10:41:01+00:00</td>
    </tr>
  </tbody>
</table>
<p>6200 rows × 3 columns</p>
</div>


---
## 3. Merging the DataFrames
<p>The data extracted comes in two separate files. Merging the two DataFrames will make it easier for us to analyze the data in the future tasks.</p>


```python
# Merge the two DataFrames
data = pd.merge(pulls,pull_files,on='pid')
data
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
      <th>pid</th>
      <th>user</th>
      <th>date</th>
      <th>file</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
      <td>test/files/pos/t5638/Among.java</td>
    </tr>
    <tr>
      <th>1</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
      <td>test/files/pos/t5638/Usage.scala</td>
    </tr>
    <tr>
      <th>2</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
      <td>test/files/pos/t9291.scala</td>
    </tr>
    <tr>
      <th>3</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
      <td>test/files/run/t8348.check</td>
    </tr>
    <tr>
      <th>4</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
      <td>test/files/run/t8348/TableColumn.java</td>
    </tr>
    <tr>
      <th>5</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
      <td>test/files/run/t8348/TableColumnImpl.java</td>
    </tr>
    <tr>
      <th>6</th>
      <td>163314316</td>
      <td>hrhino</td>
      <td>2018-01-16 23:29:16+00:00</td>
      <td>test/files/run/t8348/Test.scala</td>
    </tr>
    <tr>
      <th>7</th>
      <td>163061502</td>
      <td>joroKr21</td>
      <td>2018-01-15 23:44:52+00:00</td>
      <td>src/library/scala/concurrent/Lock.scala</td>
    </tr>
    <tr>
      <th>8</th>
      <td>163057333</td>
      <td>mkeskells</td>
      <td>2018-01-15 23:05:06+00:00</td>
      <td>src/compiler/scala/tools/nsc/profile/AsyncHelp...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>163057333</td>
      <td>mkeskells</td>
      <td>2018-01-15 23:05:06+00:00</td>
      <td>src/compiler/scala/tools/nsc/profile/Profiler....</td>
    </tr>
    <tr>
      <th>10</th>
      <td>162985594</td>
      <td>lrytz</td>
      <td>2018-01-15 15:52:39+00:00</td>
      <td>src/compiler/scala/tools/nsc/backend/jvm/BCode...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>162985594</td>
      <td>lrytz</td>
      <td>2018-01-15 15:52:39+00:00</td>
      <td>src/compiler/scala/tools/nsc/symtab/classfile/...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>162985594</td>
      <td>lrytz</td>
      <td>2018-01-15 15:52:39+00:00</td>
      <td>src/reflect/scala/reflect/internal/pickling/By...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>162838837</td>
      <td>zuvizudar</td>
      <td>2018-01-14 19:16:16+00:00</td>
      <td>src/compiler/scala/reflect/reify/phases/Calcul...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>162765232</td>
      <td>howtonotwin</td>
      <td>2018-01-13 04:37:17+00:00</td>
      <td>src/library/scala/Predef.scala</td>
    </tr>
    <tr>
      <th>15</th>
      <td>162765232</td>
      <td>howtonotwin</td>
      <td>2018-01-13 04:37:17+00:00</td>
      <td>test/files/neg/leibniz-liskov.check</td>
    </tr>
    <tr>
      <th>16</th>
      <td>162765232</td>
      <td>howtonotwin</td>
      <td>2018-01-13 04:37:17+00:00</td>
      <td>test/files/neg/leibniz-liskov.scala</td>
    </tr>
    <tr>
      <th>17</th>
      <td>162765232</td>
      <td>howtonotwin</td>
      <td>2018-01-13 04:37:17+00:00</td>
      <td>test/files/pos/leibniz-liskov.scala</td>
    </tr>
    <tr>
      <th>18</th>
      <td>162765232</td>
      <td>howtonotwin</td>
      <td>2018-01-13 04:37:17+00:00</td>
      <td>test/files/pos/leibniz_liskov.scala</td>
    </tr>
    <tr>
      <th>19</th>
      <td>162643301</td>
      <td>hrhino</td>
      <td>2018-01-12 14:29:49+00:00</td>
      <td>src/compiler/scala/tools/nsc/typechecker/Conte...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>162643301</td>
      <td>hrhino</td>
      <td>2018-01-12 14:29:49+00:00</td>
      <td>test/files/pos/parallel-classloader.scala</td>
    </tr>
    <tr>
      <th>21</th>
      <td>162643301</td>
      <td>hrhino</td>
      <td>2018-01-12 14:29:49+00:00</td>
      <td>test/files/pos/t10568/Converter.java</td>
    </tr>
    <tr>
      <th>22</th>
      <td>162643301</td>
      <td>hrhino</td>
      <td>2018-01-12 14:29:49+00:00</td>
      <td>test/files/pos/t10568/Impl.scala</td>
    </tr>
    <tr>
      <th>23</th>
      <td>162256029</td>
      <td>mkeskells</td>
      <td>2018-01-10 23:20:03+00:00</td>
      <td>src/compiler/scala/tools/nsc/backend/jvm/PostP...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>162256029</td>
      <td>mkeskells</td>
      <td>2018-01-10 23:20:03+00:00</td>
      <td>src/compiler/scala/tools/nsc/backend/jvm/analy...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>162197834</td>
      <td>SethTisue</td>
      <td>2018-01-10 18:31:25+00:00</td>
      <td>LICENSE</td>
    </tr>
    <tr>
      <th>26</th>
      <td>162197834</td>
      <td>SethTisue</td>
      <td>2018-01-10 18:31:25+00:00</td>
      <td>doc/LICENSE.md</td>
    </tr>
    <tr>
      <th>27</th>
      <td>162197834</td>
      <td>SethTisue</td>
      <td>2018-01-10 18:31:25+00:00</td>
      <td>doc/License.rtf</td>
    </tr>
    <tr>
      <th>28</th>
      <td>162197834</td>
      <td>SethTisue</td>
      <td>2018-01-10 18:31:25+00:00</td>
      <td>project/VersionUtil.scala</td>
    </tr>
    <tr>
      <th>29</th>
      <td>162197834</td>
      <td>SethTisue</td>
      <td>2018-01-10 18:31:25+00:00</td>
      <td>src/library/scala/util/Properties.scala</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>85558</th>
      <td>551651</td>
      <td>szeiger</td>
      <td>2011-12-02 13:02:29+00:00</td>
      <td>test/files/run/enums.scala</td>
    </tr>
    <tr>
      <th>85559</th>
      <td>551505</td>
      <td>szeiger</td>
      <td>2011-12-02 12:04:52+00:00</td>
      <td>src/library/scala/Enumeration.scala</td>
    </tr>
    <tr>
      <th>85560</th>
      <td>551505</td>
      <td>szeiger</td>
      <td>2011-12-02 12:04:52+00:00</td>
      <td>test/files/run/enums.check</td>
    </tr>
    <tr>
      <th>85561</th>
      <td>551505</td>
      <td>szeiger</td>
      <td>2011-12-02 12:04:52+00:00</td>
      <td>test/files/run/enums.scala</td>
    </tr>
    <tr>
      <th>85562</th>
      <td>551474</td>
      <td>szeiger</td>
      <td>2011-12-02 11:46:43+00:00</td>
      <td>src/library/scala/collection/BitSetLike.scala</td>
    </tr>
    <tr>
      <th>85563</th>
      <td>551375</td>
      <td>soc</td>
      <td>2011-12-02 11:02:49+00:00</td>
      <td>src/compiler/scala/tools/nsc/doc/html/page/Tem...</td>
    </tr>
    <tr>
      <th>85564</th>
      <td>550508</td>
      <td>soc</td>
      <td>2011-12-02 02:53:07+00:00</td>
      <td>test/files/neg/t3481.check</td>
    </tr>
    <tr>
      <th>85565</th>
      <td>550508</td>
      <td>soc</td>
      <td>2011-12-02 02:53:07+00:00</td>
      <td>test/files/neg/t3481.scala</td>
    </tr>
    <tr>
      <th>85566</th>
      <td>548900</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:46:20+00:00</td>
      <td>src/compiler/scala/tools/nsc/util/SourceFile.s...</td>
    </tr>
    <tr>
      <th>85567</th>
      <td>548838</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:35:53+00:00</td>
      <td>test/disabled/scalacheck/redblack.scala</td>
    </tr>
    <tr>
      <th>85568</th>
      <td>548838</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:35:53+00:00</td>
      <td>test/files/scalacheck/redblack.scala</td>
    </tr>
    <tr>
      <th>85569</th>
      <td>548744</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:15:58+00:00</td>
      <td>src/library/scala/util/matching/Regex.scala</td>
    </tr>
    <tr>
      <th>85570</th>
      <td>548694</td>
      <td>dcsobral</td>
      <td>2011-12-01 19:00:36+00:00</td>
      <td>src/library/scala/sys/process/package.scala</td>
    </tr>
    <tr>
      <th>85571</th>
      <td>547941</td>
      <td>xeno-by</td>
      <td>2011-12-01 16:16:42+00:00</td>
      <td>src/compiler/scala/reflect/internal/Importers....</td>
    </tr>
    <tr>
      <th>85572</th>
      <td>547941</td>
      <td>xeno-by</td>
      <td>2011-12-01 16:16:42+00:00</td>
      <td>src/compiler/scala/reflect/internal/Types.scala</td>
    </tr>
    <tr>
      <th>85573</th>
      <td>547941</td>
      <td>xeno-by</td>
      <td>2011-12-01 16:16:42+00:00</td>
      <td>src/compiler/scala/tools/nsc/transform/LiftCod...</td>
    </tr>
    <tr>
      <th>85574</th>
      <td>547941</td>
      <td>xeno-by</td>
      <td>2011-12-01 16:16:42+00:00</td>
      <td>test/pending/run/t5230.scala</td>
    </tr>
    <tr>
      <th>85575</th>
      <td>547941</td>
      <td>xeno-by</td>
      <td>2011-12-01 16:16:42+00:00</td>
      <td>test/pending/run/t5230_nolift.scala</td>
    </tr>
    <tr>
      <th>85576</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>src/compiler/scala/tools/nsc/util/BitSet.scala</td>
    </tr>
    <tr>
      <th>85577</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>src/library/scala/collection/BitSet.scala</td>
    </tr>
    <tr>
      <th>85578</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>src/library/scala/collection/BitSetLike.scala</td>
    </tr>
    <tr>
      <th>85579</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>src/library/scala/collection/SortedSet.scala</td>
    </tr>
    <tr>
      <th>85580</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>src/library/scala/collection/immutable/BitSet....</td>
    </tr>
    <tr>
      <th>85581</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>src/library/scala/collection/immutable/SortedS...</td>
    </tr>
    <tr>
      <th>85582</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>src/library/scala/collection/mutable/BitSet.scala</td>
    </tr>
    <tr>
      <th>85583</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>test/files/run/bitsets.check</td>
    </tr>
    <tr>
      <th>85584</th>
      <td>547354</td>
      <td>szeiger</td>
      <td>2011-12-01 14:03:23+00:00</td>
      <td>test/files/run/bitsets.scala</td>
    </tr>
    <tr>
      <th>85585</th>
      <td>547150</td>
      <td>odersky</td>
      <td>2011-12-01 12:39:40+00:00</td>
      <td>src/compiler/scala/tools/nsc/transform/LiftCod...</td>
    </tr>
    <tr>
      <th>85586</th>
      <td>547150</td>
      <td>odersky</td>
      <td>2011-12-01 12:39:40+00:00</td>
      <td>src/library/scala/reflect/api/Trees.scala</td>
    </tr>
    <tr>
      <th>85587</th>
      <td>547003</td>
      <td>robinst</td>
      <td>2011-12-01 11:42:26+00:00</td>
      <td>README.rst</td>
    </tr>
  </tbody>
</table>
<p>85588 rows × 4 columns</p>
</div>


---
## 4. Is the project still actively maintained?
<p>The activity in an open source project is not very consistent. Some projects might be active for many years after the initial release, while others can slowly taper out into oblivion. Before committing to contributing to a project, it is important to understand the state of the project. Is development going steadily, or is there a drop? Has the project been abandoned altogether?</p>
<p>The data used in this project was collected in January of 2018. I am interested in the evolution of the number of contributions up to that date.</p>
<p>For Scala, I will do this by plotting a chart of the project's activity. I will calculate the number of pull requests submitted each (calendar) month during the project's lifetime. I will then plot these numbers to see the trend of contributions.</p>


```python
%matplotlib inline

# Create a column that will store the month and the year, as a string
pulls['month_year'] = pulls.apply(lambda x: str(x['date'].month) + '-' + str(x['date'].year),axis=1)

# Group by month_year and count the pull requests
counts = pulls.groupby('month_year').size().sort_index()

# Plot the results
counts.plot.bar()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a14d2aac50>




![png](/assets/img/github-scala-repo-analysis_files/github-scala-repo-analysis_7_1.png)

---
## 5. Is there camaraderie in the project?
<p>The organizational structure varies from one project to another, and it can influence your success as a contributor. A project that has a very small community might not be the best one to start working on. The small community might indicate a high barrier of entry. This can be caused by several factors, including a community that is reluctant to accept pull requests from "outsiders," that the code base is hard to work with, etc. However, a large community can serve as an indicator that the project is regularly accepting pull requests from new contributors. Such a project would be a good place to start.</p>
<p>In order to evaluate the dynamics of the community, I will plot a histogram of the number of pull requests submitted by each user. A distribution that shows that there are few people that only contribute a small number of pull requests can be used as in indicator that the project is not welcoming of new contributors. </p>


```python
# Required for matplotlib
%matplotlib inline

# Group by the submitter
by_user = pulls.groupby('user').count()['pid']

# Plot the histogram
by_user.plot.hist()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a14a3954a8>




![png](/assets/img/github-scala-repo-analysis_files/github-scala-repo-analysis_9_1.png)

---
## 6. What files were changed in the last ten pull requests?
<p>Choosing the right place to make a contribution is as important as choosing the project to contribute to. Some parts of the code might be stable, some might be dead. Contributing there might not have the most impact. Therefore it is important to understand the parts of the system that have been recently changed. This allows us to pinpoint the "hot" areas of the code where most of the activity is happening. Focusing on those parts might not be the most effective use of our times.</p>


```python
# Identify the last 10 pull requests
last_10 = pulls.nlargest(columns='date',n=10)

# Join the two data sets
joined_pr = pd.merge(last_10,pull_files,on='pid')

# Identify the unique files
files = set(joined_pr.file)

# Print the results
files
```




    {'LICENSE',
     'doc/LICENSE.md',
     'doc/License.rtf',
     'project/VersionUtil.scala',
     'src/compiler/scala/reflect/reify/phases/Calculate.scala',
     'src/compiler/scala/tools/nsc/backend/jvm/BCodeHelpers.scala',
     'src/compiler/scala/tools/nsc/backend/jvm/PostProcessor.scala',
     'src/compiler/scala/tools/nsc/backend/jvm/analysis/BackendUtils.scala',
     'src/compiler/scala/tools/nsc/profile/AsyncHelper.scala',
     'src/compiler/scala/tools/nsc/profile/Profiler.scala',
     'src/compiler/scala/tools/nsc/symtab/classfile/ClassfileParser.scala',
     'src/compiler/scala/tools/nsc/typechecker/Contexts.scala',
     'src/library/scala/Predef.scala',
     'src/library/scala/concurrent/Lock.scala',
     'src/library/scala/util/Properties.scala',
     'src/reflect/scala/reflect/internal/pickling/ByteCodecs.scala',
     'src/reflect/scala/reflect/internal/tpe/GlbLubs.scala',
     'src/scaladoc/scala/tools/nsc/doc/html/page/Entity.scala',
     'src/scalap/decoder.properties',
     'test/files/neg/leibniz-liskov.check',
     'test/files/neg/leibniz-liskov.scala',
     'test/files/pos/leibniz-liskov.scala',
     'test/files/pos/leibniz_liskov.scala',
     'test/files/pos/parallel-classloader.scala',
     'test/files/pos/t10568/Converter.java',
     'test/files/pos/t10568/Impl.scala',
     'test/files/pos/t10686.scala',
     'test/files/pos/t5638/Among.java',
     'test/files/pos/t5638/Usage.scala',
     'test/files/pos/t9291.scala',
     'test/files/run/t8348.check',
     'test/files/run/t8348/TableColumn.java',
     'test/files/run/t8348/TableColumnImpl.java',
     'test/files/run/t8348/Test.scala'}


---
## 7. Who made the most pull requests to a given file?
<p>When contributing to a project, we might need some guidance. We might find ourselves needing some information regarding the codebase. It is important to direct any questions to the right person. Contributors to open source projects generally have other day jobs, so their time is limited. It is important to address our questions to the right people. One way to identify the right target for our inquiries is by using their contribution history.</p>
<p>We identified <code>src/compiler/scala/reflect/reify/phases/Calculate.scala</code> as being recently changed. We are interested in the top 3 developers who changed that file. Those developers are the ones most likely to have the best understanding of the code.</p>


```python
# This is the file we are interested in:
file = 'src/compiler/scala/reflect/reify/phases/Calculate.scala'

# Identify the commits that changed the file
file_pr = data.loc[data.file == file]

# Count the number of changes made by each developer
author_counts = file_pr.groupby('user').size()

# Print the top 3 developers
author_counts.nlargest(3)
```




    user
    xeno-by     11
    retronym     5
    soc          4
    dtype: int64


---
## 8. Who made the last ten pull requests on a given file?
<p>Open source projects suffer from fluctuating membership. This makes the problem of finding the right person more challenging: the person has to be knowledgeable <em>and</em> still be involved in the project. A person that contributed a lot in the past might no longer be available (or willing) to help. To get a better understanding, we need to investigate the more recent history of that particular part of the system. </p>
<p>Like in the previous task, we will look at the history of  <code>src/compiler/scala/reflect/reify/phases/Calculate.scala</code>.</p>


```python
file = 'src/compiler/scala/reflect/reify/phases/Calculate.scala'

# Select the pull requests that changed the target file
file_pr = pull_files.loc[pull_files.file == file]

# Merge the obtained results with the pulls DataFrame
joined_pr = pd.merge(file_pr,pulls,on='pid')

# Find the users of the last 10 most recent pull requests
users_last_10 = set(joined_pr.nlargest(columns='date',n=10)['user'])

# Printing the results
users_last_10
```




    {'bjornregnell', 'retronym', 'soc', 'starblood', 'xeno-by', 'zuvizudar'}


---
## 9. The pull requests of two special developers
<p>Now that we have identified two potential contacts in the projects, we need to find the person who was most involved in the project in recent times. That person is most likely to answer our questions. For each calendar year, we are interested in understanding the number of pull requests the authors submitted. This will give us a high-level image of their contribution trend to the project.</p>


```python
%matplotlib inline

# The developers we are interested in
authors = ['xeno-by', 'soc']

# Get all the developers' pull requests
by_author = pulls[pulls.user.isin(authors)]

# Count the number of pull requests submitted each year
counts = by_author.groupby(['user', by_author['date'].dt.year]).agg({'pid': 'count'}).reset_index()

# Convert the table to a wide format
counts_wide = counts.pivot_table(index='date', columns='user', values='pid', fill_value=0)

# Plot the results
counts_wide.plot.bar()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a14e6834e0>




![png](/assets/img/github-scala-repo-analysis_files/github-scala-repo-analysis_17_1.png)

---
## 10. Visualizing the contributions of each developer
<p>As mentioned before, it is important to make a distinction between the global expertise and contribution levels and the contribution levels at a more granular level (file, submodule, etc.) In our case, we want to see which of our two developers of interest have the most experience with the code in a given file. We will measure experience by the number of pull requests submitted that affect that file and how recent those pull requests were submitted.</p>


```python
authors = ['xeno-by', 'soc']
file = 'src/compiler/scala/reflect/reify/phases/Calculate.scala'

# Select the pull requests submitted by the authors, from the `data` DataFrame
by_author = data[data.user.isin(authors)]

# Select the pull requests that affect the file
by_file = by_author[by_author.file == file]

# Group and count the number of PRs done by each user each year
grouped = by_file.groupby(['user', by_file['date'].dt.year]).count()['pid'].reset_index()

# Transform the data into a wide format
by_file_wide = grouped.pivot_table(index='date', columns=['user'], values='pid', fill_value=0)

# Plot the results
by_file_wide.plot(kind='bar')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a14f0b0d68>




![png](/assets/img/github-scala-repo-analysis_files/github-scala-repo-analysis_19_1.png)


---
## Dig deeper?

You can find out more about this project at [Github](https://github.com/Kau5h1K/github-scala-repo-analysis).
{: .notice}
