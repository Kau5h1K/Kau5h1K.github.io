
## 1. The brief
<p>Imagine working for a digital marketing agency, and the agency is approached by a massive online retailer of furniture. They want to test our skills at creating large campaigns for all of their website. We are tasked with creating a prototype set of keywords for search campaigns for their sofas section. The client says that they want us to generate keywords for the following products: </p>
<ul>
<li>sofas</li>
<li>convertible sofas</li>
<li>love seats</li>
<li>recliners</li>
<li>sofa beds</li>
</ul>
<p><strong>The brief</strong>: The client is generally a low-cost retailer, offering many promotions and discounts. We will need to focus on such keywords. We will also need to move away from luxury keywords and topics, as we are targeting price-sensitive customers. Because we are going to be tight on budget, it would be good to focus on a tightly targeted set of keywords and make sure they are all set to exact and phrase match.</p>
<p>Based on the brief above we will first need to generate a list of words, that together with the products given above would make for good keywords. Here are some examples:</p>
<ul>
<li>Products: sofas, recliners</li>
<li>Words: buy, prices</li>
</ul>
<p>The resulting keywords: 'buy sofas', 'sofas buy', 'buy recliners', 'recliners buy',
          'prices sofas', 'sofas prices', 'prices recliners', 'recliners prices'.</p>
<p>As a final result, we want to have a DataFrame that looks like this: </p>
<table>
<thead>
<tr>
<th>Campaign</th>
<th>Ad Group</th>
<th>Keyword</th>
<th>Criterion Type</th>
</tr>
</thead>
<tbody>
<tr>
<td>Campaign1</td>
<td>AdGroup_1</td>
<td>keyword 1a</td>
<td>Exact</td>
</tr>
<tr>
<td>Campaign1</td>
<td>AdGroup_1</td>
<td>keyword 1a</td>
<td>Phrase</td>
</tr>
<tr>
<td>Campaign1</td>
<td>AdGroup_1</td>
<td>keyword 1b</td>
<td>Exact</td>
</tr>
<tr>
<td>Campaign1</td>
<td>AdGroup_1</td>
<td>keyword 1b</td>
<td>Phrase</td>
</tr>
<tr>
<td>Campaign1</td>
<td>AdGroup_2</td>
<td>keyword 2a</td>
<td>Exact</td>
</tr>
<tr>
<td>Campaign1</td>
<td>AdGroup_2</td>
<td>keyword 2a</td>
<td>Phrase</td>
</tr>
</tbody>
</table>
<p>The first step is to come up with a list of words that users might use to express their desire in buying low-cost sofas.</p>


```python
# List of words to pair with products
words = ['buy','price','discount','promotion','promo','shop','budget','ecommerce','offers','sale']

# Print list of words
print(words)
```

    ['buy', 'price', 'discount', 'promotion', 'promo', 'shop', 'budget', 'ecommerce', 'offers', 'sale']
    

## 2. Combine the words with the product names
<p>Imagining all the possible combinations of keywords can be stressful! But not for us, because we are keyword ninjas! We know how to translate campaign briefs into Python data structures and can imagine the resulting DataFrames that we need to create.</p>
<p>Now that we have brainstormed the words that work well with the brief that we received, it is now time to combine them with the product names to generate meaningful search keywords. We want to combine every word with every product once before, and once after, as seen in the example above.</p>
<p>As a quick reminder, for the product 'recliners' and the words 'buy' and 'price' for example, we would want to generate the following combinations: </p>
<p>buy recliners<br>
recliners buy<br>
price recliners<br>
recliners price<br>
...  </p>
<p>and so on for all the words and products that we have.</p>


```python
products = ['sofas', 'convertible sofas', 'love seats', 'recliners', 'sofa beds']

# Create an empty list
keywords_list = []

# Loop through products
for product in products:
    # Loop through words
    for word in words:
        # Append combinations
        keywords_list.append([product, product + ' ' + word])
        keywords_list.append([product, word + ' ' + product])
        
# Inspect keyword list
from pprint import pprint
pprint(keywords_list)
```

    [['sofas', 'sofas buy'],
     ['sofas', 'buy sofas'],
     ['sofas', 'sofas price'],
     ['sofas', 'price sofas'],
     ['sofas', 'sofas discount'],
     ['sofas', 'discount sofas'],
     ['sofas', 'sofas promotion'],
     ['sofas', 'promotion sofas'],
     ['sofas', 'sofas promo'],
     ['sofas', 'promo sofas'],
     ['sofas', 'sofas shop'],
     ['sofas', 'shop sofas'],
     ['sofas', 'sofas budget'],
     ['sofas', 'budget sofas'],
     ['sofas', 'sofas ecommerce'],
     ['sofas', 'ecommerce sofas'],
     ['sofas', 'sofas offers'],
     ['sofas', 'offers sofas'],
     ['sofas', 'sofas sale'],
     ['sofas', 'sale sofas'],
     ['convertible sofas', 'convertible sofas buy'],
     ['convertible sofas', 'buy convertible sofas'],
     ['convertible sofas', 'convertible sofas price'],
     ['convertible sofas', 'price convertible sofas'],
     ['convertible sofas', 'convertible sofas discount'],
     ['convertible sofas', 'discount convertible sofas'],
     ['convertible sofas', 'convertible sofas promotion'],
     ['convertible sofas', 'promotion convertible sofas'],
     ['convertible sofas', 'convertible sofas promo'],
     ['convertible sofas', 'promo convertible sofas'],
     ['convertible sofas', 'convertible sofas shop'],
     ['convertible sofas', 'shop convertible sofas'],
     ['convertible sofas', 'convertible sofas budget'],
     ['convertible sofas', 'budget convertible sofas'],
     ['convertible sofas', 'convertible sofas ecommerce'],
     ['convertible sofas', 'ecommerce convertible sofas'],
     ['convertible sofas', 'convertible sofas offers'],
     ['convertible sofas', 'offers convertible sofas'],
     ['convertible sofas', 'convertible sofas sale'],
     ['convertible sofas', 'sale convertible sofas'],
     ['love seats', 'love seats buy'],
     ['love seats', 'buy love seats'],
     ['love seats', 'love seats price'],
     ['love seats', 'price love seats'],
     ['love seats', 'love seats discount'],
     ['love seats', 'discount love seats'],
     ['love seats', 'love seats promotion'],
     ['love seats', 'promotion love seats'],
     ['love seats', 'love seats promo'],
     ['love seats', 'promo love seats'],
     ['love seats', 'love seats shop'],
     ['love seats', 'shop love seats'],
     ['love seats', 'love seats budget'],
     ['love seats', 'budget love seats'],
     ['love seats', 'love seats ecommerce'],
     ['love seats', 'ecommerce love seats'],
     ['love seats', 'love seats offers'],
     ['love seats', 'offers love seats'],
     ['love seats', 'love seats sale'],
     ['love seats', 'sale love seats'],
     ['recliners', 'recliners buy'],
     ['recliners', 'buy recliners'],
     ['recliners', 'recliners price'],
     ['recliners', 'price recliners'],
     ['recliners', 'recliners discount'],
     ['recliners', 'discount recliners'],
     ['recliners', 'recliners promotion'],
     ['recliners', 'promotion recliners'],
     ['recliners', 'recliners promo'],
     ['recliners', 'promo recliners'],
     ['recliners', 'recliners shop'],
     ['recliners', 'shop recliners'],
     ['recliners', 'recliners budget'],
     ['recliners', 'budget recliners'],
     ['recliners', 'recliners ecommerce'],
     ['recliners', 'ecommerce recliners'],
     ['recliners', 'recliners offers'],
     ['recliners', 'offers recliners'],
     ['recliners', 'recliners sale'],
     ['recliners', 'sale recliners'],
     ['sofa beds', 'sofa beds buy'],
     ['sofa beds', 'buy sofa beds'],
     ['sofa beds', 'sofa beds price'],
     ['sofa beds', 'price sofa beds'],
     ['sofa beds', 'sofa beds discount'],
     ['sofa beds', 'discount sofa beds'],
     ['sofa beds', 'sofa beds promotion'],
     ['sofa beds', 'promotion sofa beds'],
     ['sofa beds', 'sofa beds promo'],
     ['sofa beds', 'promo sofa beds'],
     ['sofa beds', 'sofa beds shop'],
     ['sofa beds', 'shop sofa beds'],
     ['sofa beds', 'sofa beds budget'],
     ['sofa beds', 'budget sofa beds'],
     ['sofa beds', 'sofa beds ecommerce'],
     ['sofa beds', 'ecommerce sofa beds'],
     ['sofa beds', 'sofa beds offers'],
     ['sofa beds', 'offers sofa beds'],
     ['sofa beds', 'sofa beds sale'],
     ['sofa beds', 'sale sofa beds']]
    

## 3. Convert the list of lists into a DataFrame
<p>Now we want to convert this list of lists into a DataFrame so we can easily manipulate it and manage the final output.</p>


```python
# Load library
import pandas as pd
# Create a DataFrame from list
#keywords_df = pd.from_records(keywords_list)
keywords_df = pd.DataFrame(keywords_list)
# Print the keywords DataFrame to explore it
print(keywords_df)
```

                        0                            1
    0               sofas                    sofas buy
    1               sofas                    buy sofas
    2               sofas                  sofas price
    3               sofas                  price sofas
    4               sofas               sofas discount
    5               sofas               discount sofas
    6               sofas              sofas promotion
    7               sofas              promotion sofas
    8               sofas                  sofas promo
    9               sofas                  promo sofas
    10              sofas                   sofas shop
    11              sofas                   shop sofas
    12              sofas                 sofas budget
    13              sofas                 budget sofas
    14              sofas              sofas ecommerce
    15              sofas              ecommerce sofas
    16              sofas                 sofas offers
    17              sofas                 offers sofas
    18              sofas                   sofas sale
    19              sofas                   sale sofas
    20  convertible sofas        convertible sofas buy
    21  convertible sofas        buy convertible sofas
    22  convertible sofas      convertible sofas price
    23  convertible sofas      price convertible sofas
    24  convertible sofas   convertible sofas discount
    25  convertible sofas   discount convertible sofas
    26  convertible sofas  convertible sofas promotion
    27  convertible sofas  promotion convertible sofas
    28  convertible sofas      convertible sofas promo
    29  convertible sofas      promo convertible sofas
    ..                ...                          ...
    70          recliners               recliners shop
    71          recliners               shop recliners
    72          recliners             recliners budget
    73          recliners             budget recliners
    74          recliners          recliners ecommerce
    75          recliners          ecommerce recliners
    76          recliners             recliners offers
    77          recliners             offers recliners
    78          recliners               recliners sale
    79          recliners               sale recliners
    80          sofa beds                sofa beds buy
    81          sofa beds                buy sofa beds
    82          sofa beds              sofa beds price
    83          sofa beds              price sofa beds
    84          sofa beds           sofa beds discount
    85          sofa beds           discount sofa beds
    86          sofa beds          sofa beds promotion
    87          sofa beds          promotion sofa beds
    88          sofa beds              sofa beds promo
    89          sofa beds              promo sofa beds
    90          sofa beds               sofa beds shop
    91          sofa beds               shop sofa beds
    92          sofa beds             sofa beds budget
    93          sofa beds             budget sofa beds
    94          sofa beds          sofa beds ecommerce
    95          sofa beds          ecommerce sofa beds
    96          sofa beds             sofa beds offers
    97          sofa beds             offers sofa beds
    98          sofa beds               sofa beds sale
    99          sofa beds               sale sofa beds
    
    [100 rows x 2 columns]
    

## 4. Rename the columns of the DataFrame
<p>Before we can upload this table of keywords, we will need to give the columns meaningful names. If we inspect the DataFrame we just created above, we can see that the columns are currently named <code>0</code> and <code>1</code>. <code>Ad Group</code> (example: "sofas") and <code>Keyword</code> (example: "sofas buy") are much more appropriate names.</p>


```python
# Rename the columns of the DataFrame
keywords_df = keywords_df.rename({0:'Ad Group',1:'Keyword'},axis='columns')
```

## 5. Add a campaign column
<p>Now we need to add some additional information to our DataFrame. 
We need a new column called <code>Campaign</code> for the campaign name. We want campaign names to be descriptive of our group of keywords and products, so let's call this campaign 'SEM_Sofas'.</p>


```python
# Add a campaign column
keywords_df = keywords_df.assign(Campaign = 'SEM_Sofas')
```

## 6. Create the match type column
<p>There are different keyword match types. One is exact match, which is for matching the exact term or are close variations of that exact term. Another match type is broad match, which means ads may show on searches that include misspellings, synonyms, related searches, and other relevant variations.</p>
<p>Straight from Google's AdWords <a href="https://support.google.com/google-ads/answer/2497836?hl=en">documentation</a>:</p>
<blockquote>
  <p>In general, the broader the match type, the more traffic potential that keyword will have, since your ads may be triggered more often. Conversely, a narrower match type means that your ads may show less often—but when they do, they’re likely to be more related to someone’s search.</p>
</blockquote>
<p>Since the client is tight on budget, we want to make sure all the keywords are in exact match at the beginning.</p>


```python
# Add a criterion type column
keywords_df.insert(len(keywords_df.columns),'Criterion Type','Exact')
keywords_df
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
      <th>Ad Group</th>
      <th>Keyword</th>
      <th>Campaign</th>
      <th>Criterion Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>sofas</td>
      <td>sofas buy</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>1</th>
      <td>sofas</td>
      <td>buy sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>2</th>
      <td>sofas</td>
      <td>sofas price</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>3</th>
      <td>sofas</td>
      <td>price sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>4</th>
      <td>sofas</td>
      <td>sofas discount</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>5</th>
      <td>sofas</td>
      <td>discount sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>6</th>
      <td>sofas</td>
      <td>sofas promotion</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>7</th>
      <td>sofas</td>
      <td>promotion sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>8</th>
      <td>sofas</td>
      <td>sofas promo</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>9</th>
      <td>sofas</td>
      <td>promo sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>10</th>
      <td>sofas</td>
      <td>sofas shop</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>11</th>
      <td>sofas</td>
      <td>shop sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>12</th>
      <td>sofas</td>
      <td>sofas budget</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>13</th>
      <td>sofas</td>
      <td>budget sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>14</th>
      <td>sofas</td>
      <td>sofas ecommerce</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>15</th>
      <td>sofas</td>
      <td>ecommerce sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>16</th>
      <td>sofas</td>
      <td>sofas offers</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>17</th>
      <td>sofas</td>
      <td>offers sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>18</th>
      <td>sofas</td>
      <td>sofas sale</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>19</th>
      <td>sofas</td>
      <td>sale sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>20</th>
      <td>convertible sofas</td>
      <td>convertible sofas buy</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>21</th>
      <td>convertible sofas</td>
      <td>buy convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>22</th>
      <td>convertible sofas</td>
      <td>convertible sofas price</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>23</th>
      <td>convertible sofas</td>
      <td>price convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>24</th>
      <td>convertible sofas</td>
      <td>convertible sofas discount</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>25</th>
      <td>convertible sofas</td>
      <td>discount convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>26</th>
      <td>convertible sofas</td>
      <td>convertible sofas promotion</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>27</th>
      <td>convertible sofas</td>
      <td>promotion convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>28</th>
      <td>convertible sofas</td>
      <td>convertible sofas promo</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>29</th>
      <td>convertible sofas</td>
      <td>promo convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>recliners</td>
      <td>recliners shop</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>71</th>
      <td>recliners</td>
      <td>shop recliners</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>72</th>
      <td>recliners</td>
      <td>recliners budget</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>73</th>
      <td>recliners</td>
      <td>budget recliners</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>74</th>
      <td>recliners</td>
      <td>recliners ecommerce</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>75</th>
      <td>recliners</td>
      <td>ecommerce recliners</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>76</th>
      <td>recliners</td>
      <td>recliners offers</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>77</th>
      <td>recliners</td>
      <td>offers recliners</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>78</th>
      <td>recliners</td>
      <td>recliners sale</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>79</th>
      <td>recliners</td>
      <td>sale recliners</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>80</th>
      <td>sofa beds</td>
      <td>sofa beds buy</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>81</th>
      <td>sofa beds</td>
      <td>buy sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>82</th>
      <td>sofa beds</td>
      <td>sofa beds price</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>83</th>
      <td>sofa beds</td>
      <td>price sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>84</th>
      <td>sofa beds</td>
      <td>sofa beds discount</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>85</th>
      <td>sofa beds</td>
      <td>discount sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>86</th>
      <td>sofa beds</td>
      <td>sofa beds promotion</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>87</th>
      <td>sofa beds</td>
      <td>promotion sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>88</th>
      <td>sofa beds</td>
      <td>sofa beds promo</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>89</th>
      <td>sofa beds</td>
      <td>promo sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>90</th>
      <td>sofa beds</td>
      <td>sofa beds shop</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>91</th>
      <td>sofa beds</td>
      <td>shop sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>92</th>
      <td>sofa beds</td>
      <td>sofa beds budget</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>93</th>
      <td>sofa beds</td>
      <td>budget sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>94</th>
      <td>sofa beds</td>
      <td>sofa beds ecommerce</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>95</th>
      <td>sofa beds</td>
      <td>ecommerce sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>96</th>
      <td>sofa beds</td>
      <td>sofa beds offers</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>97</th>
      <td>sofa beds</td>
      <td>offers sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>98</th>
      <td>sofa beds</td>
      <td>sofa beds sale</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>99</th>
      <td>sofa beds</td>
      <td>sale sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 4 columns</p>
</div>



## 7. Duplicate all the keywords into 'phrase' match
<p>The great thing about exact match is that it is very specific, and we can control the process very well. The tradeoff, however, is that:  </p>
<ol>
<li>The search volume for exact match is lower than other match types</li>
<li>We can't possibly think of all the ways in which people search, and so, we are probably missing out on some high-quality keywords.</li>
</ol>
<p>So it's good to use another match called <em>phrase match</em> as a discovery mechanism to allow our ads to be triggered by keywords that include our exact match keywords, together with anything before (or after) them.</p>
<p>Later on, when we launch the campaign, we can explore with modified broad match, broad match, and negative match types, for better visibility and control of our campaigns.</p>


```python
# Make a copy of the keywords DataFrame
keywords_phrase = keywords_df.copy()

# Change criterion type match to phrase
keywords_phrase['Criterion Type'] = 'Phrase'
# Append the DataFrames
keywords_df_final = keywords_df.append(keywords_phrase)
keywords_df_final
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
      <th>Ad Group</th>
      <th>Keyword</th>
      <th>Campaign</th>
      <th>Criterion Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>sofas</td>
      <td>sofas buy</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>1</th>
      <td>sofas</td>
      <td>buy sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>2</th>
      <td>sofas</td>
      <td>sofas price</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>3</th>
      <td>sofas</td>
      <td>price sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>4</th>
      <td>sofas</td>
      <td>sofas discount</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>5</th>
      <td>sofas</td>
      <td>discount sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>6</th>
      <td>sofas</td>
      <td>sofas promotion</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>7</th>
      <td>sofas</td>
      <td>promotion sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>8</th>
      <td>sofas</td>
      <td>sofas promo</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>9</th>
      <td>sofas</td>
      <td>promo sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>10</th>
      <td>sofas</td>
      <td>sofas shop</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>11</th>
      <td>sofas</td>
      <td>shop sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>12</th>
      <td>sofas</td>
      <td>sofas budget</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>13</th>
      <td>sofas</td>
      <td>budget sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>14</th>
      <td>sofas</td>
      <td>sofas ecommerce</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>15</th>
      <td>sofas</td>
      <td>ecommerce sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>16</th>
      <td>sofas</td>
      <td>sofas offers</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>17</th>
      <td>sofas</td>
      <td>offers sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>18</th>
      <td>sofas</td>
      <td>sofas sale</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>19</th>
      <td>sofas</td>
      <td>sale sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>20</th>
      <td>convertible sofas</td>
      <td>convertible sofas buy</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>21</th>
      <td>convertible sofas</td>
      <td>buy convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>22</th>
      <td>convertible sofas</td>
      <td>convertible sofas price</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>23</th>
      <td>convertible sofas</td>
      <td>price convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>24</th>
      <td>convertible sofas</td>
      <td>convertible sofas discount</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>25</th>
      <td>convertible sofas</td>
      <td>discount convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>26</th>
      <td>convertible sofas</td>
      <td>convertible sofas promotion</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>27</th>
      <td>convertible sofas</td>
      <td>promotion convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>28</th>
      <td>convertible sofas</td>
      <td>convertible sofas promo</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>29</th>
      <td>convertible sofas</td>
      <td>promo convertible sofas</td>
      <td>SEM_Sofas</td>
      <td>Exact</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>recliners</td>
      <td>recliners shop</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>71</th>
      <td>recliners</td>
      <td>shop recliners</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>72</th>
      <td>recliners</td>
      <td>recliners budget</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>73</th>
      <td>recliners</td>
      <td>budget recliners</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>74</th>
      <td>recliners</td>
      <td>recliners ecommerce</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>75</th>
      <td>recliners</td>
      <td>ecommerce recliners</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>76</th>
      <td>recliners</td>
      <td>recliners offers</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>77</th>
      <td>recliners</td>
      <td>offers recliners</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>78</th>
      <td>recliners</td>
      <td>recliners sale</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>79</th>
      <td>recliners</td>
      <td>sale recliners</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>80</th>
      <td>sofa beds</td>
      <td>sofa beds buy</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>81</th>
      <td>sofa beds</td>
      <td>buy sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>82</th>
      <td>sofa beds</td>
      <td>sofa beds price</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>83</th>
      <td>sofa beds</td>
      <td>price sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>84</th>
      <td>sofa beds</td>
      <td>sofa beds discount</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>85</th>
      <td>sofa beds</td>
      <td>discount sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>86</th>
      <td>sofa beds</td>
      <td>sofa beds promotion</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>87</th>
      <td>sofa beds</td>
      <td>promotion sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>88</th>
      <td>sofa beds</td>
      <td>sofa beds promo</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>89</th>
      <td>sofa beds</td>
      <td>promo sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>90</th>
      <td>sofa beds</td>
      <td>sofa beds shop</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>91</th>
      <td>sofa beds</td>
      <td>shop sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>92</th>
      <td>sofa beds</td>
      <td>sofa beds budget</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>93</th>
      <td>sofa beds</td>
      <td>budget sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>94</th>
      <td>sofa beds</td>
      <td>sofa beds ecommerce</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>95</th>
      <td>sofa beds</td>
      <td>ecommerce sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>96</th>
      <td>sofa beds</td>
      <td>sofa beds offers</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>97</th>
      <td>sofa beds</td>
      <td>offers sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>98</th>
      <td>sofa beds</td>
      <td>sofa beds sale</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
    <tr>
      <th>99</th>
      <td>sofa beds</td>
      <td>sale sofa beds</td>
      <td>SEM_Sofas</td>
      <td>Phrase</td>
    </tr>
  </tbody>
</table>
<p>200 rows × 4 columns</p>
</div>



## 8. Save and summarize!
<p>To upload our campaign, we need to save it as a CSV file. Then we will be able to import it to AdWords editor or BingAds editor. There is also the option of pasting the data into the editor if we want, but having easy access to the saved data is great so let's save to a CSV file!</p>
<p>Looking at a summary of our campaign structure is good now that we've wrapped up our keyword work. We can do that by grouping by ad group and criterion type and counting by keyword. This summary shows us that we assigned specific keywords to specific ad groups, which are each part of a campaign. In essence, we are telling Google (or Bing, etc.) that we want any of the words in each ad group to trigger one of the ads in the same ad group. Separately, we will have to create another table for ads, which is a task for another day and would look something like this:</p>
<table>
<thead>
<tr>
<th>Campaign</th>
<th>Ad Group</th>
<th>Headline 1</th>
<th>Headline 2</th>
<th>Description</th>
<th>Final URL</th>
</tr>
</thead>
<tbody>
<tr>
<td>SEM_Sofas</td>
<td>Sofas</td>
<td>Looking for Quality Sofas?</td>
<td>Explore Our Massive Collection</td>
<td>30-day Returns With Free Delivery Within the US. Start Shopping Now</td>
<td>DataCampSofas.com/sofas</td>
</tr>
<tr>
<td>SEM_Sofas</td>
<td>Sofas</td>
<td>Looking for Affordable Sofas?</td>
<td>Check Out Our Weekly Offers</td>
<td>30-day Returns With Free Delivery Within the US. Start Shopping Now</td>
<td>DataCampSofas.com/sofas</td>
</tr>
<tr>
<td>SEM_Sofas</td>
<td>Recliners</td>
<td>Looking for Quality Recliners?</td>
<td>Explore Our Massive Collection</td>
<td>30-day Returns With Free Delivery Within the US. Start Shopping Now</td>
<td>DataCampSofas.com/recliners</td>
</tr>
<tr>
<td>SEM_Sofas</td>
<td>Recliners</td>
<td>Need Affordable Recliners?</td>
<td>Check Out Our Weekly Offers</td>
<td>30-day Returns With Free Delivery Within the US. Start Shopping Now</td>
<td>DataCampSofas.com/recliners</td>
</tr>
</tbody>
</table>
<p>Together, these tables get us the sample <strong>keywords -> ads -> landing pages</strong> mapping shown in the diagram below.</p>
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_400/img/kwds_ads_lpages.png" alt="Keywords-Ads-Landing pages flow"></p>


```python
# Save the final keywords to a CSV file
keywords_df_final.to_csv('keywords.csv',index=False)
# View a summary of our campaign work
summary = keywords_df_final.groupby(['Ad Group', 'Criterion Type'])['Keyword'].count()
print(summary)
```

    Ad Group           Criterion Type
    convertible sofas  Exact             20
                       Phrase            20
    love seats         Exact             20
                       Phrase            20
    recliners          Exact             20
                       Phrase            20
    sofa beds          Exact             20
                       Phrase            20
    sofas              Exact             20
                       Phrase            20
    Name: Keyword, dtype: int64
    
