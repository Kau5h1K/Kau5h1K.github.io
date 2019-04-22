
## 1. The NIST Special Publication 800-63B
<p>If you – 50 years ago – needed to come up with a secret password you were probably part of a secret espionage organization or (more likely) you were pretending to be a spy when playing as a kid. Today, many of us are forced to come up with new passwords <em>all the time</em> when signing into sites and apps. As a password <em>inventeur</em> it is your responsibility to come up with good, hard-to-crack passwords. But it is also in the interest of sites and apps to make sure that you use good passwords. The problem is that it's really hard to define what makes a good password. However, <em>the National Institute of Standards and Technology</em> (NIST) knows what the second best thing is: To make sure you're at least not using a <em>bad</em> password. </p>
<p>In this notebook, we will go through the rules in <a href="https://pages.nist.gov/800-63-3/sp800-63b.html">NIST Special Publication 800-63B</a> which details what checks a <em>verifier</em> (what the NIST calls a second party responsible for storing and verifying passwords) should perform to make sure users don't pick bad passwords. We will go through the passwords of users from a fictional company and use python to flag the users with bad passwords. But us being able to do this already means the fictional company is breaking one of the rules of 800-63B:</p>
<blockquote>
  <p>Verifiers SHALL store memorized secrets in a form that is resistant to offline attacks. Memorized secrets SHALL be salted and hashed using a suitable one-way key derivation function.</p>
</blockquote>
<p>That is, never save users' passwords in plaintext, always encrypt the passwords! Keeping this in mind for the next time we're building a password management system, let's load in the data.</p>
<p><em>Warning: The list of passwords and the fictional user database both contain <strong>real</strong> passwords leaked from <strong>real</strong> websites. These passwords have not been filtered in any way and include words that are explicit, derogatory and offensive.</em></p>


```python
# Importing the pandas module
import pandas as pd
# Loading in datasets/users.csv 
users = pd.read_csv('datasets/users.csv')

# Printing out how many users we've got
print(len(users))
# Taking a look at the 12 first users
users.head(12)
```

    982
    




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
      <th>id</th>
      <th>user_name</th>
      <th>password</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>vance.jennings</td>
      <td>joobheco</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>consuelo.eaton</td>
      <td>0869347314</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>mitchel.perkins</td>
      <td>fabypotter</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>odessa.vaughan</td>
      <td>aharney88</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>araceli.wilder</td>
      <td>acecdn3000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>shawn.harrington</td>
      <td>5278049</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>evelyn.gay</td>
      <td>master</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>noreen.hale</td>
      <td>murphy</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>gladys.ward</td>
      <td>lwsves2</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>brant.zimmerman</td>
      <td>1190KAREN5572497</td>
    </tr>
    <tr>
      <th>10</th>
      <td>11</td>
      <td>leanna.abbott</td>
      <td>aivlys24</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>milford.hubbard</td>
      <td>hubbard</td>
    </tr>
  </tbody>
</table>
</div>



## 2. Passwords should not be too short
<p>If we take a look at the first 12 users above we already see some bad passwords. But let's not get ahead of ourselves and start flagging passwords <em>manually</em>. What is the first thing we should check according to the NIST Special Publication 800-63B?</p>
<blockquote>
  <p>Verifiers SHALL require subscriber-chosen memorized secrets to be at least 8 characters in length.</p>
</blockquote>
<p>Ok, so the passwords of our users shouldn't be too short. Let's start by checking that!</p>


```python
# Calculating the lengths of users' passwords
users['length'] = users['password'].str.len()

# Flagging the users with too short passwords
users['too_short'] = users['length'] < 8

# Counting and printing the number of users with too short passwords
print(users['too_short'].sum())
# Taking a look at the 12 first rows
users.head(12)
```

    376
    




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
      <th>id</th>
      <th>user_name</th>
      <th>password</th>
      <th>length</th>
      <th>too_short</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>vance.jennings</td>
      <td>joobheco</td>
      <td>8</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>consuelo.eaton</td>
      <td>0869347314</td>
      <td>10</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>mitchel.perkins</td>
      <td>fabypotter</td>
      <td>10</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>odessa.vaughan</td>
      <td>aharney88</td>
      <td>9</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>araceli.wilder</td>
      <td>acecdn3000</td>
      <td>10</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>shawn.harrington</td>
      <td>5278049</td>
      <td>7</td>
      <td>True</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>evelyn.gay</td>
      <td>master</td>
      <td>6</td>
      <td>True</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>noreen.hale</td>
      <td>murphy</td>
      <td>6</td>
      <td>True</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>gladys.ward</td>
      <td>lwsves2</td>
      <td>7</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>brant.zimmerman</td>
      <td>1190KAREN5572497</td>
      <td>16</td>
      <td>False</td>
    </tr>
    <tr>
      <th>10</th>
      <td>11</td>
      <td>leanna.abbott</td>
      <td>aivlys24</td>
      <td>8</td>
      <td>False</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>milford.hubbard</td>
      <td>hubbard</td>
      <td>7</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



## 3.  Common passwords people use
<p>Already this simple rule flagged a couple of offenders among the first 12 users. Next up in Special Publication 800-63B is the rule that</p>
<blockquote>
  <p>verifiers SHALL compare the prospective secrets against a list that contains values known to be commonly-used, expected, or compromised.</p>
  <ul>
  <li>Passwords obtained from previous breach corpuses.</li>
  <li>Dictionary words.</li>
  <li>Repetitive or sequential characters (e.g. ‘aaaaaa’, ‘1234abcd’).</li>
  <li>Context-specific words, such as the name of the service, the username, and derivatives thereof.</li>
  </ul>
</blockquote>
<p>We're going to check these in order and start with <em>Passwords obtained from previous breach corpuses</em>, that is, websites where hackers have leaked all the users' passwords. As many websites don't follow the NIST guidelines and encrypt passwords there now exist large lists of the most popular passwords. Let's start by loading in the 10,000 most common passwords which I've taken from <a href="https://github.com/danielmiessler/SecLists/tree/master/Passwords">here</a>.</p>


```python
# Reading in the top 10000 passwords
common_passwords = pd.read_csv('datasets/10_million_password_list_top_10000.txt',header=None,squeeze=True)

# Taking a look at the top 20
common_passwords.head(20)
```




    0        123456
    1      password
    2      12345678
    3        qwerty
    4     123456789
    5         12345
    6          1234
    7        111111
    8       1234567
    9        dragon
    10       123123
    11     baseball
    12       abc123
    13     football
    14       monkey
    15      letmein
    16       696969
    17       shadow
    18       master
    19       666666
    Name: 0, dtype: object



## 4.  Passwords should not be common passwords
<p>The list of passwords was ordered, with the most common passwords first, and so we shouldn't be surprised to see passwords like <code>123456</code> and <code>qwerty</code> above. As hackers also have access to this list of common passwords, it's important that none of our users use these passwords!</p>
<p>Let's flag all the passwords in our user database that are among the top 10,000 used passwords.</p>


```python
# Flagging the users with passwords that are common passwords
users['common_password'] = users['password'].isin(common_passwords[:10000])

# Counting and printing the number of users using common passwords
print(users['common_password'].sum())
# Taking a look at the 12 first rows
users.head(12)
```

    129
    




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
      <th>id</th>
      <th>user_name</th>
      <th>password</th>
      <th>length</th>
      <th>too_short</th>
      <th>common_password</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>vance.jennings</td>
      <td>joobheco</td>
      <td>8</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>consuelo.eaton</td>
      <td>0869347314</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>mitchel.perkins</td>
      <td>fabypotter</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>odessa.vaughan</td>
      <td>aharney88</td>
      <td>9</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>araceli.wilder</td>
      <td>acecdn3000</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>shawn.harrington</td>
      <td>5278049</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>evelyn.gay</td>
      <td>master</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>noreen.hale</td>
      <td>murphy</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>gladys.ward</td>
      <td>lwsves2</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>brant.zimmerman</td>
      <td>1190KAREN5572497</td>
      <td>16</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>10</th>
      <td>11</td>
      <td>leanna.abbott</td>
      <td>aivlys24</td>
      <td>8</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>milford.hubbard</td>
      <td>hubbard</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



## 5. Passwords should not be common words
<p>Ay ay ay! It turns out many of our users use common passwords, and of the first 12 users there are already two. However, as most common passwords also tend to be short, they were already flagged as being too short. What is the next thing we should check?</p>
<blockquote>
  <p>Verifiers SHALL compare the prospective secrets against a list that contains [...] dictionary words.</p>
</blockquote>
<p>This follows the same logic as before: It is easy for hackers to check users' passwords against common English words and therefore common English words make bad passwords. Let's check our users' passwords against the top 10,000 English words from <a href="https://github.com/first20hours/google-10000-english">Google's Trillion Word Corpus</a>.</p>


```python
# Reading in a list of the 10000 most common words
words = pd.read_csv('datasets/google-10000-english.txt',header=None,squeeze=True)

# Flagging the users with passwords that are common words
users['common_word'] = users['password'].str.lower().isin(words)

# Counting and printing the number of users using common words as passwords
print(users['common_word'].sum())
# Taking a look at the 12 first rows
users.head(12)
```

    137
    




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
      <th>id</th>
      <th>user_name</th>
      <th>password</th>
      <th>length</th>
      <th>too_short</th>
      <th>common_password</th>
      <th>common_word</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>vance.jennings</td>
      <td>joobheco</td>
      <td>8</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>consuelo.eaton</td>
      <td>0869347314</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>mitchel.perkins</td>
      <td>fabypotter</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>odessa.vaughan</td>
      <td>aharney88</td>
      <td>9</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>araceli.wilder</td>
      <td>acecdn3000</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>shawn.harrington</td>
      <td>5278049</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>evelyn.gay</td>
      <td>master</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>noreen.hale</td>
      <td>murphy</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>gladys.ward</td>
      <td>lwsves2</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>brant.zimmerman</td>
      <td>1190KAREN5572497</td>
      <td>16</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>10</th>
      <td>11</td>
      <td>leanna.abbott</td>
      <td>aivlys24</td>
      <td>8</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>milford.hubbard</td>
      <td>hubbard</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



## 6. Passwords should not be your name
<p>It turns out many of our passwords were common English words too! Next up on the NIST list:</p>
<blockquote>
  <p>Verifiers SHALL compare the prospective secrets against a list that contains [...] context-specific words, such as the name of the service, the username, and derivatives thereof.</p>
</blockquote>
<p>Ok, so there are many things we could check here. One thing to notice is that our users' usernames consist of their first names and last names separated by a dot. For now, let's just flag passwords that are the same as either a user's first or last name.</p>


```python
# Extracting first and last names into their own columns
users['first_name'] = users['user_name'].str.extract(r'(^\w+)', expand = False)
users['last_name'] = users['user_name'].str.extract(r'(\w+$)', expand = False)
#users['first_name'] = users['user_name'].str.split('.').str.get(0)
#users['last_name'] = users['user_name'].str.split('.').str.get(1)

# Flagging the users with passwords that matches their names
users['uses_name'] = ((users['password'] == users['first_name']) | (users['password'] == users['last_name']))

# Counting and printing the number of users using names as passwords
print(users['uses_name'].sum())

# Taking a look at the 12 first rows
users.head(12)
```

    50
    




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
      <th>id</th>
      <th>user_name</th>
      <th>password</th>
      <th>length</th>
      <th>too_short</th>
      <th>common_password</th>
      <th>common_word</th>
      <th>first_name</th>
      <th>last_name</th>
      <th>uses_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>vance.jennings</td>
      <td>joobheco</td>
      <td>8</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>vance</td>
      <td>jennings</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>consuelo.eaton</td>
      <td>0869347314</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>consuelo</td>
      <td>eaton</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>mitchel.perkins</td>
      <td>fabypotter</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>mitchel</td>
      <td>perkins</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>odessa.vaughan</td>
      <td>aharney88</td>
      <td>9</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>odessa</td>
      <td>vaughan</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>araceli.wilder</td>
      <td>acecdn3000</td>
      <td>10</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>araceli</td>
      <td>wilder</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>shawn.harrington</td>
      <td>5278049</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>shawn</td>
      <td>harrington</td>
      <td>False</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>evelyn.gay</td>
      <td>master</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>evelyn</td>
      <td>gay</td>
      <td>False</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>noreen.hale</td>
      <td>murphy</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>noreen</td>
      <td>hale</td>
      <td>False</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>gladys.ward</td>
      <td>lwsves2</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>gladys</td>
      <td>ward</td>
      <td>False</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>brant.zimmerman</td>
      <td>1190KAREN5572497</td>
      <td>16</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>brant</td>
      <td>zimmerman</td>
      <td>False</td>
    </tr>
    <tr>
      <th>10</th>
      <td>11</td>
      <td>leanna.abbott</td>
      <td>aivlys24</td>
      <td>8</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>leanna</td>
      <td>abbott</td>
      <td>False</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>milford.hubbard</td>
      <td>hubbard</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>milford</td>
      <td>hubbard</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



## 7. Passwords should not be repetitive
<p>Milford Hubbard (user number 12 above), what where you thinking!? Ok, so the last thing we are going to check is a bit tricky:</p>
<blockquote>
  <p>verifiers SHALL compare the prospective secrets [so that they don't contain] repetitive or sequential characters (e.g. ‘aaaaaa’, ‘1234abcd’).</p>
</blockquote>
<p>This is tricky to check because what is <em>repetitive</em> is hard to define. Is <code>11111</code> repetitive? Yes! Is <code>12345</code> repetitive? Well, kind of. Is <code>13579</code> repetitive? Maybe not..? To check for <em>repetitiveness</em> can be arbitrarily complex, but here we're only going to do something simple. We're going to flag all passwords that contain 4 or more repeated characters.</p>


```python
### Flagging the users with passwords with >= 4 repeats
#users['too_many_repeats'] = users['password'].str.contains(r'(.)\1{3,}')
users['too_many_repeats'] = users['password'].str.contains(r'(.)\1\1\1')

# Taking a look at the users with too many repeats
users.query('too_many_repeats == True')
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
      <th>id</th>
      <th>user_name</th>
      <th>password</th>
      <th>length</th>
      <th>too_short</th>
      <th>common_password</th>
      <th>common_word</th>
      <th>first_name</th>
      <th>last_name</th>
      <th>uses_name</th>
      <th>too_many_repeats</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>146</th>
      <td>147</td>
      <td>patti.dixon</td>
      <td>555555</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>patti</td>
      <td>dixon</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>572</th>
      <td>573</td>
      <td>cornelia.bradley</td>
      <td>555555</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>cornelia</td>
      <td>bradley</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>644</th>
      <td>645</td>
      <td>essie.lopez</td>
      <td>11111</td>
      <td>5</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>essie</td>
      <td>lopez</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>798</th>
      <td>799</td>
      <td>charley.key</td>
      <td>888888</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>charley</td>
      <td>key</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>807</th>
      <td>808</td>
      <td>thurman.osborne</td>
      <td>rinnnng0</td>
      <td>8</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>thurman</td>
      <td>osborne</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>941</th>
      <td>942</td>
      <td>mitch.ferguson</td>
      <td>aaaaaa</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>mitch</td>
      <td>ferguson</td>
      <td>False</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



## 8. All together now!
<p>Now we have implemented all the basic tests for bad passwords suggested by NIST Special Publication 800-63B! What's left is just to flag all bad passwords and maybe to send these users an e-mail that strongly suggests they change their password.</p>


```python
# Flagging all passwords that are bad
#users['bad_password'] = users[['too_short','common_password','common_word','uses_name','too_many_repeats']].sum(axis=1).astype('bool')
users['bad_password'] = users['too_short'] | users['common_password'] | users['common_word'] | users['uses_name'] | users['too_many_repeats']
# Counting and printing the number of bad passwords
print(users['bad_password'].sum())
# Looking at the first 25 bad passwords
users.query('bad_password == True').head(25)
```

    424
    




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
      <th>id</th>
      <th>user_name</th>
      <th>password</th>
      <th>length</th>
      <th>too_short</th>
      <th>common_password</th>
      <th>common_word</th>
      <th>first_name</th>
      <th>last_name</th>
      <th>uses_name</th>
      <th>too_many_repeats</th>
      <th>bad_password</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>shawn.harrington</td>
      <td>5278049</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>shawn</td>
      <td>harrington</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>evelyn.gay</td>
      <td>master</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>evelyn</td>
      <td>gay</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>noreen.hale</td>
      <td>murphy</td>
      <td>6</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>noreen</td>
      <td>hale</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>gladys.ward</td>
      <td>lwsves2</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>gladys</td>
      <td>ward</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>milford.hubbard</td>
      <td>hubbard</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>milford</td>
      <td>hubbard</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>13</th>
      <td>14</td>
      <td>jamie.cochran</td>
      <td>310356</td>
      <td>6</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>jamie</td>
      <td>cochran</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>15</th>
      <td>16</td>
      <td>lorrie.gay</td>
      <td>oZ4k0QE</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>lorrie</td>
      <td>gay</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>16</th>
      <td>17</td>
      <td>domingo.dyer</td>
      <td>chelsea</td>
      <td>7</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>domingo</td>
      <td>dyer</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>17</th>
      <td>18</td>
      <td>martin.pacheco</td>
      <td>zvc1939</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>martin</td>
      <td>pacheco</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>18</th>
      <td>19</td>
      <td>shelby.massey</td>
      <td>nickgd</td>
      <td>6</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>shelby</td>
      <td>massey</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>21</th>
      <td>22</td>
      <td>leticia.sanford</td>
      <td>cocacola</td>
      <td>8</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>leticia</td>
      <td>sanford</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>22</th>
      <td>23</td>
      <td>jenny.woodard</td>
      <td>woodard</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>jenny</td>
      <td>woodard</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>25</th>
      <td>26</td>
      <td>dianna.munoz</td>
      <td>AJ9Da</td>
      <td>5</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>dianna</td>
      <td>munoz</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>26</th>
      <td>27</td>
      <td>julia.savage</td>
      <td>ewokzs</td>
      <td>6</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>julia</td>
      <td>savage</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>28</th>
      <td>29</td>
      <td>joaquin.walters</td>
      <td>YyGjz8E</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>joaquin</td>
      <td>walters</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>30</th>
      <td>31</td>
      <td>rosanna.reid</td>
      <td>reid</td>
      <td>4</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>rosanna</td>
      <td>reid</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>34</th>
      <td>35</td>
      <td>roger.golden</td>
      <td>jOYZBs8</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>roger</td>
      <td>golden</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>38</th>
      <td>39</td>
      <td>gus.padilla</td>
      <td>wwewwf1</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>gus</td>
      <td>padilla</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>43</th>
      <td>44</td>
      <td>vito.nicholson</td>
      <td>225377</td>
      <td>6</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>vito</td>
      <td>nicholson</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>45</th>
      <td>46</td>
      <td>morris.price</td>
      <td>NdZ7E6</td>
      <td>6</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>morris</td>
      <td>price</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>47</th>
      <td>48</td>
      <td>jarred.white</td>
      <td>CQB3Z</td>
      <td>5</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>jarred</td>
      <td>white</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>48</th>
      <td>49</td>
      <td>junior.burch</td>
      <td>diffo</td>
      <td>5</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>junior</td>
      <td>burch</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>51</th>
      <td>52</td>
      <td>ahmad.hopper</td>
      <td>123456789</td>
      <td>9</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>ahmad</td>
      <td>hopper</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>52</th>
      <td>53</td>
      <td>rosario.merrill</td>
      <td>y8uM7D6</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>rosario</td>
      <td>merrill</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>56</th>
      <td>57</td>
      <td>dwayne.hurst</td>
      <td>mikeloo</td>
      <td>7</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>dwayne</td>
      <td>hurst</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



## 9. Otherwise, the password should be up to the user
<p>In this notebook, we've implemented the password checks recommended by the NIST Special Publication 800-63B. It's certainly possible to better implement these checks, for example, by using a longer list of common passwords. Also note that the NIST checks in no way guarantee that a chosen password is good, just that it's not obviously bad.</p>
<p>Apart from the checks we've implemented above the NIST is also clear with what password rules should <em>not</em> be imposed:</p>
<blockquote>
  <p>Verifiers SHOULD NOT impose other composition rules (e.g., requiring mixtures of different character types or prohibiting consecutively repeated characters) for memorized secrets. Verifiers SHOULD NOT require memorized secrets to be changed arbitrarily (e.g., periodically).</p>
</blockquote>
<p>So the next time a website or app tells you to "include both a number, symbol and an upper and lower case character in your password" you should send them a copy of <a href="https://pages.nist.gov/800-63-3/sp800-63b.html">NIST Special Publication 800-63B</a>.</p>


```python
# Enter a password that passes the NIST requirements
# PLEASE DO NOT USE AN EXISTING PASSWORD HERE
new_password = "qwerty123"
```
