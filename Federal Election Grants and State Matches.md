# Analysis of the last three federal election administration appropriations to states via the EAC (2018, 2020, and as part of the CARES Act)

This data was pulled from the [Election Assistance Commission](https://www.eac.gov/payments-and-grants/2020-cares-act-grants), run through [Tabula](https://tabula.technology/) to scrape into a csv, and cleaned and formatted in MS Excel before the files were uploaded here for analysis.

These figures represent funds appropriated to states and the match required to qualify for the grants. The match requirements are percentages of the grant. In 2018 there was a 5 percent match requirement, and in 2020 and the CARES Act, there was a 20 percent match requirement.

Here we'll import the relevant libraries:


```python
import pandas as pd
import numpy as np
```

Import the [2018 election security grants](https://www.eac.gov/payments-and-grants/hava-funds-state-chart-view):


```python
HAVA_2018 = pd.read_csv("tabula-2018_HAVA_Election_Security_Funds.csv", dtype={'Grantee': str, 'Federal Share': int, 'State Match': int, 'Total Award': int})
```

Quick look to make sure it worked:


```python
HAVA_2018.head()
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
      <th>Grantee</th>
      <th>Federal Share</th>
      <th>State Match</th>
      <th>Total Award</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
    </tr>
  </tbody>
</table>
</div>



Import the [2020 election security grant](https://www.eac.gov/payments-and-grants/2020-hava-funds) totals:


```python
HAVA_2020 = pd.read_csv("tabula-2020HAVA_State_Allocation_Chart_with_Match-1.csv", dtype={'State': str, 'Federal Share': int, 'State Share 20%': int, 'Total': int})
```

Make sure it worked:


```python
HAVA_2020.head()
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
      <th>State</th>
      <th>Federal Share</th>
      <th>State Share 20%</th>
      <th>Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
    </tr>
  </tbody>
</table>
</div>



Combine the two:


```python
both_yrs_raw = pd.merge(HAVA_2018, HAVA_2020, left_on="Grantee", right_on="State")
```

Wanted to look at what that created to make sure I got it all:


```python
both_yrs_raw
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
      <th>Grantee</th>
      <th>Federal Share_x</th>
      <th>State Match</th>
      <th>Total Award</th>
      <th>State</th>
      <th>Federal Share_y</th>
      <th>State Share 20%</th>
      <th>Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>Alabama</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>Arizona</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>Arkansas</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>California</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Colorado</td>
      <td>6342979</td>
      <td>317149</td>
      <td>6660128</td>
      <td>Colorado</td>
      <td>7106267</td>
      <td>1421253</td>
      <td>8527521</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Connecticut</td>
      <td>5120554</td>
      <td>256028</td>
      <td>5376582</td>
      <td>Connecticut</td>
      <td>5735740</td>
      <td>1147148</td>
      <td>6882887</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>Delaware</td>
      <td>3031481</td>
      <td>606296</td>
      <td>3637777</td>
    </tr>
    <tr>
      <th>8</th>
      <td>District of Columbia</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>District of Columbia</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Florida</td>
      <td>19187003</td>
      <td>959350</td>
      <td>20146353</td>
      <td>Florida</td>
      <td>21506406</td>
      <td>4301281</td>
      <td>25807687</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Georgia</td>
      <td>10305783</td>
      <td>515289</td>
      <td>10821072</td>
      <td>Georgia</td>
      <td>11549183</td>
      <td>2309837</td>
      <td>13859020</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Hawaii</td>
      <td>3134080</td>
      <td>156704</td>
      <td>3290784</td>
      <td>Hawaii</td>
      <td>3508595</td>
      <td>701719</td>
      <td>4210314</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Idaho</td>
      <td>3229896</td>
      <td>161495</td>
      <td>3391391</td>
      <td>Idaho</td>
      <td>3616020</td>
      <td>723204</td>
      <td>4339223</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illinois</td>
      <td>13232290</td>
      <td>661615</td>
      <td>13893905</td>
      <td>Illinois</td>
      <td>14830251</td>
      <td>2966050</td>
      <td>17796302</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Indiana</td>
      <td>7595088</td>
      <td>379754</td>
      <td>7974842</td>
      <td>Indiana</td>
      <td>8510075</td>
      <td>1702015</td>
      <td>10212090</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Iowa</td>
      <td>4608084</td>
      <td>230404</td>
      <td>4838488</td>
      <td>Iowa</td>
      <td>5161182</td>
      <td>1032236</td>
      <td>6193418</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Kansas</td>
      <td>4383595</td>
      <td>219180</td>
      <td>4602774</td>
      <td>Kansas</td>
      <td>4909494</td>
      <td>981899</td>
      <td>5891393</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Kentucky</td>
      <td>5773423</td>
      <td>288671</td>
      <td>6062094</td>
      <td>Kentucky</td>
      <td>6467707</td>
      <td>1293541</td>
      <td>7761248</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Louisiana</td>
      <td>5889487</td>
      <td>294474</td>
      <td>6183961</td>
      <td>Louisiana</td>
      <td>6597832</td>
      <td>1319566</td>
      <td>7917398</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Maine</td>
      <td>3130979</td>
      <td>156549</td>
      <td>3287528</td>
      <td>Maine</td>
      <td>3505118</td>
      <td>701024</td>
      <td>4206142</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Maryland</td>
      <td>7063699</td>
      <td>353185</td>
      <td>7416884</td>
      <td>Maryland</td>
      <td>7914306</td>
      <td>1582861</td>
      <td>9497167</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Massachusetts</td>
      <td>7890854</td>
      <td>394543</td>
      <td>8285396</td>
      <td>Massachusetts</td>
      <td>8841674</td>
      <td>1768335</td>
      <td>10610009</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Michigan</td>
      <td>10706992</td>
      <td>535350</td>
      <td>11242341</td>
      <td>Michigan</td>
      <td>11999001</td>
      <td>2399800</td>
      <td>14398801</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Minnesota</td>
      <td>6595610</td>
      <td>329781</td>
      <td>6925391</td>
      <td>Minnesota</td>
      <td>7389506</td>
      <td>1477901</td>
      <td>8867407</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Mississippi</td>
      <td>4483541</td>
      <td>224177</td>
      <td>4707718</td>
      <td>Mississippi</td>
      <td>5021549</td>
      <td>1004310</td>
      <td>6025859</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Missouri</td>
      <td>7230625</td>
      <td>361531</td>
      <td>7592156</td>
      <td>Missouri</td>
      <td>8101455</td>
      <td>1620291</td>
      <td>9721746</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Montana</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>Montana</td>
      <td>3127978</td>
      <td>625596</td>
      <td>3753573</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Nebraska</td>
      <td>3496936</td>
      <td>174847</td>
      <td>3671783</td>
      <td>Nebraska</td>
      <td>3915413</td>
      <td>783083</td>
      <td>4698495</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Nevada</td>
      <td>4277723</td>
      <td>213886</td>
      <td>4491609</td>
      <td>Nevada</td>
      <td>4790795</td>
      <td>958159</td>
      <td>5748954</td>
    </tr>
    <tr>
      <th>29</th>
      <td>New Hampshire</td>
      <td>3102253</td>
      <td>155113</td>
      <td>3257366</td>
      <td>New Hampshire</td>
      <td>3472912</td>
      <td>694582</td>
      <td>4167494</td>
    </tr>
    <tr>
      <th>30</th>
      <td>New Jersey</td>
      <td>9757450</td>
      <td>487873</td>
      <td>10245323</td>
      <td>New Jersey</td>
      <td>10934419</td>
      <td>2186884</td>
      <td>13121302</td>
    </tr>
    <tr>
      <th>31</th>
      <td>New Mexico</td>
      <td>3699470</td>
      <td>184973</td>
      <td>3884443</td>
      <td>New Mexico</td>
      <td>4142484</td>
      <td>828497</td>
      <td>4970981</td>
    </tr>
    <tr>
      <th>32</th>
      <td>New York</td>
      <td>19483647</td>
      <td>974182</td>
      <td>20457829</td>
      <td>New York</td>
      <td>21838990</td>
      <td>4367798</td>
      <td>26206788</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Carolina</td>
      <td>10373237</td>
      <td>518662</td>
      <td>10891899</td>
      <td>North Carolina</td>
      <td>11624810</td>
      <td>2324962</td>
      <td>13949772</td>
    </tr>
    <tr>
      <th>34</th>
      <td>North Dakota</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>North Dakota</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Ohio</td>
      <td>12186021</td>
      <td>609301</td>
      <td>12795322</td>
      <td>Ohio</td>
      <td>13657222</td>
      <td>2731444</td>
      <td>16388666</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Oklahoma</td>
      <td>5196017</td>
      <td>259801</td>
      <td>5455818</td>
      <td>Oklahoma</td>
      <td>5820345</td>
      <td>1164069</td>
      <td>6984414</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Oregon</td>
      <td>5362981</td>
      <td>268149</td>
      <td>5631130</td>
      <td>Oregon</td>
      <td>6007538</td>
      <td>1201508</td>
      <td>7209045</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pennsylvania</td>
      <td>13476156</td>
      <td>673808</td>
      <td>14149964</td>
      <td>Pennsylvania</td>
      <td>15103663</td>
      <td>3020733</td>
      <td>18124395</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Puerto Rico</td>
      <td>3676962</td>
      <td>183848</td>
      <td>3860810</td>
      <td>Puerto Rico</td>
      <td>5821405</td>
      <td>1164281</td>
      <td>6985686</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Rhode Island</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>Rhode Island</td>
      <td>3210168</td>
      <td>642034</td>
      <td>3852202</td>
    </tr>
    <tr>
      <th>41</th>
      <td>South Carolina</td>
      <td>6040794</td>
      <td>302040</td>
      <td>6342834</td>
      <td>South Carolina</td>
      <td>6767471</td>
      <td>1353494</td>
      <td>8120966</td>
    </tr>
    <tr>
      <th>42</th>
      <td>South Dakota</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>South Dakota</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Tennessee</td>
      <td>7565418</td>
      <td>378271</td>
      <td>7943689</td>
      <td>Tennessee</td>
      <td>8476810</td>
      <td>1695362</td>
      <td>10172173</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Texas</td>
      <td>23252604</td>
      <td>1162630</td>
      <td>24415234</td>
      <td>Texas</td>
      <td>26064574</td>
      <td>5212915</td>
      <td>31277489</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Utah</td>
      <td>4111052</td>
      <td>205553</td>
      <td>4316605</td>
      <td>Utah</td>
      <td>4603931</td>
      <td>920786</td>
      <td>5524718</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Vermont</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>Vermont</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Virginia</td>
      <td>9080731</td>
      <td>454037</td>
      <td>9534767</td>
      <td>Virginia</td>
      <td>10175711</td>
      <td>2035142</td>
      <td>12210853</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Washington</td>
      <td>7907768</td>
      <td>395388</td>
      <td>8303156</td>
      <td>Washington</td>
      <td>8860638</td>
      <td>1772128</td>
      <td>10632765</td>
    </tr>
    <tr>
      <th>49</th>
      <td>West Virginia</td>
      <td>3611943</td>
      <td>180597</td>
      <td>3792540</td>
      <td>West Virginia</td>
      <td>4044353</td>
      <td>808871</td>
      <td>4853223</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Wisconsin</td>
      <td>6978318</td>
      <td>348916</td>
      <td>7327234</td>
      <td>Wisconsin</td>
      <td>7818581</td>
      <td>1563716</td>
      <td>9382297</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Wyoming</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>Wyoming</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
  </tbody>
</table>
</div>



Make sure my data types were proper for some math:


```python
both_yrs_raw.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 52 entries, 0 to 51
    Data columns (total 8 columns):
     #   Column           Non-Null Count  Dtype 
    ---  ------           --------------  ----- 
     0   Grantee          52 non-null     object
     1   Federal Share_x  52 non-null     int64 
     2   State Match      52 non-null     int64 
     3   Total Award      52 non-null     int64 
     4   State            52 non-null     object
     5   Federal Share_y  52 non-null     int64 
     6   State Share 20%  52 non-null     int64 
     7   Total            52 non-null     int64 
    dtypes: int64(6), object(2)
    memory usage: 3.7+ KB


Needed to start trimming this down. Removed the "State" column which was redundant:


```python
trim_1 = both_yrs_raw[['Grantee', 'Federal Share_x', 'State Match', 'Total Award', 'Federal Share_y', 'State Share 20%', 'Total']]
```

Made sure that worked:


```python
trim_1.head()
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
      <th>Grantee</th>
      <th>Federal Share_x</th>
      <th>State Match</th>
      <th>Total Award</th>
      <th>Federal Share_y</th>
      <th>State Share 20%</th>
      <th>Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
    </tr>
  </tbody>
</table>
</div>



Wanted to rename the columns to be more obvious, so "sm" here refers to the required state match:


```python
trim_1.columns = ['state', 'fed_share_18', 'sm_18', 'total_18', 'fed_share_20', 'sm_20', 'total_20']
```

Checking my work again:


```python
trim_1.head()
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
      <th>state</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
    </tr>
  </tbody>
</table>
</div>



Added a new column to combine the state match totals from 2018 and 2020:


```python
trim_1['total_match']=trim_1['sm_18'] + trim_1['sm_20']
```

Checked my work:


```python
trim_1.head()
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
      <th>state</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
      <th>total_match</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
      <td>1688332</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>750000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
      <td>2045732</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
      <td>1226149</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
      <td>9476075</td>
    </tr>
  </tbody>
</table>
</div>



Ran a sort to see the top 10 total combined match totals by state for 2018 and 2020:


```python
trim_1.sort_values("total_match", ascending=False).head(10)
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
      <th>state</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
      <th>total_match</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
      <td>9476075</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Texas</td>
      <td>23252604</td>
      <td>1162630</td>
      <td>24415234</td>
      <td>26064574</td>
      <td>5212915</td>
      <td>31277489</td>
      <td>6375545</td>
    </tr>
    <tr>
      <th>32</th>
      <td>New York</td>
      <td>19483647</td>
      <td>974182</td>
      <td>20457829</td>
      <td>21838990</td>
      <td>4367798</td>
      <td>26206788</td>
      <td>5341980</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Florida</td>
      <td>19187003</td>
      <td>959350</td>
      <td>20146353</td>
      <td>21506406</td>
      <td>4301281</td>
      <td>25807687</td>
      <td>5260631</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pennsylvania</td>
      <td>13476156</td>
      <td>673808</td>
      <td>14149964</td>
      <td>15103663</td>
      <td>3020733</td>
      <td>18124395</td>
      <td>3694541</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illinois</td>
      <td>13232290</td>
      <td>661615</td>
      <td>13893905</td>
      <td>14830251</td>
      <td>2966050</td>
      <td>17796302</td>
      <td>3627665</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Ohio</td>
      <td>12186021</td>
      <td>609301</td>
      <td>12795322</td>
      <td>13657222</td>
      <td>2731444</td>
      <td>16388666</td>
      <td>3340745</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Michigan</td>
      <td>10706992</td>
      <td>535350</td>
      <td>11242341</td>
      <td>11999001</td>
      <td>2399800</td>
      <td>14398801</td>
      <td>2935150</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Carolina</td>
      <td>10373237</td>
      <td>518662</td>
      <td>10891899</td>
      <td>11624810</td>
      <td>2324962</td>
      <td>13949772</td>
      <td>2843624</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Georgia</td>
      <td>10305783</td>
      <td>515289</td>
      <td>10821072</td>
      <td>11549183</td>
      <td>2309837</td>
      <td>13859020</td>
      <td>2825126</td>
    </tr>
  </tbody>
</table>
</div>



Realized I needed to add in the [CARES Act totals](https://www.eac.gov/payments-and-grants/2020-cares-act-grants). Imported that here:


```python
cares_act_approp = pd.read_csv("tabula-State_Allocations_of_Supplemental_CARES_Funds.csv", dtype={'State': str, 'Federal Share': int, 'State Share at 20%': int, 'Total': int})
```

Looked at the raw upload:


```python
cares_act_approp.head()
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
      <th>State</th>
      <th>Federal Share</th>
      <th>State Share at\r20%</th>
      <th>Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6473611</td>
      <td>1294722</td>
      <td>7768334</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7842119</td>
      <td>1568424</td>
      <td>9410542</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4703886</td>
      <td>940777</td>
      <td>5644663</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36293345</td>
      <td>7258669</td>
      <td>43552014</td>
    </tr>
  </tbody>
</table>
</div>



Cleaned up the column names:


```python
cares_act_approp.columns = ['state', 'fed_share_cares', 'state_share_cares', 'total']
```

Made sure that worked:


```python
cares_act_approp.head()
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
      <th>state</th>
      <th>fed_share_cares</th>
      <th>state_share_cares</th>
      <th>total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6473611</td>
      <td>1294722</td>
      <td>7768334</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7842119</td>
      <td>1568424</td>
      <td>9410542</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4703886</td>
      <td>940777</td>
      <td>5644663</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36293345</td>
      <td>7258669</td>
      <td>43552014</td>
    </tr>
  </tbody>
</table>
</div>



Let's get all three batches of grants into one dataset:


```python
all_three = pd.merge(cares_act_approp, trim_1, on="state")
```

Make sure that worked:


```python
all_three.head(10)
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
      <th>state</th>
      <th>fed_share_cares</th>
      <th>state_share_cares</th>
      <th>total</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
      <th>total_match</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6473611</td>
      <td>1294722</td>
      <td>7768334</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
      <td>1688332</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>750000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7842119</td>
      <td>1568424</td>
      <td>9410542</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
      <td>2045732</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4703886</td>
      <td>940777</td>
      <td>5644663</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
      <td>1226149</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36293345</td>
      <td>7258669</td>
      <td>43552014</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
      <td>9476075</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Colorado</td>
      <td>6665335</td>
      <td>1333067</td>
      <td>7998402</td>
      <td>6342979</td>
      <td>317149</td>
      <td>6660128</td>
      <td>7106267</td>
      <td>1421253</td>
      <td>8527521</td>
      <td>1738402</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Connecticut</td>
      <td>5381732</td>
      <td>1076346</td>
      <td>6458079</td>
      <td>5120554</td>
      <td>256028</td>
      <td>5376582</td>
      <td>5735740</td>
      <td>1147148</td>
      <td>6882887</td>
      <td>1403176</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3031481</td>
      <td>606296</td>
      <td>3637777</td>
      <td>756296</td>
    </tr>
    <tr>
      <th>8</th>
      <td>District of Columbia</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>750000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Florida</td>
      <td>20152160</td>
      <td>4030432</td>
      <td>24182592</td>
      <td>19187003</td>
      <td>959350</td>
      <td>20146353</td>
      <td>21506406</td>
      <td>4301281</td>
      <td>25807687</td>
      <td>5260631</td>
    </tr>
  </tbody>
</table>
</div>



Going to need to update our "total_match" figures:


```python
all_three['total_match'] = all_three['state_share_cares'] + all_three['sm_18'] + all_three['sm_20']
```

Let's take a look:


```python
all_three.head()
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
      <th>state</th>
      <th>fed_share_cares</th>
      <th>state_share_cares</th>
      <th>total</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
      <th>total_match</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6473611</td>
      <td>1294722</td>
      <td>7768334</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
      <td>2983054</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>1350000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7842119</td>
      <td>1568424</td>
      <td>9410542</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
      <td>3614156</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4703886</td>
      <td>940777</td>
      <td>5644663</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
      <td>2166926</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36293345</td>
      <td>7258669</td>
      <td>43552014</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
      <td>16734744</td>
    </tr>
  </tbody>
</table>
</div>



We'll run another sort to get a total on state match requirements:


```python
all_three.sort_values("total_match", ascending=False).head(10)
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
      <th>state</th>
      <th>fed_share_cares</th>
      <th>state_share_cares</th>
      <th>total</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
      <th>total_match</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36293345</td>
      <td>7258669</td>
      <td>43552014</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
      <td>16734744</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Texas</td>
      <td>24421231</td>
      <td>4884246</td>
      <td>29305478</td>
      <td>23252604</td>
      <td>1162630</td>
      <td>24415234</td>
      <td>26064574</td>
      <td>5212915</td>
      <td>31277489</td>
      <td>11259791</td>
    </tr>
    <tr>
      <th>32</th>
      <td>New York</td>
      <td>20463651</td>
      <td>4092730</td>
      <td>24556381</td>
      <td>19483647</td>
      <td>974182</td>
      <td>20457829</td>
      <td>21838990</td>
      <td>4367798</td>
      <td>26206788</td>
      <td>9434710</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Florida</td>
      <td>20152160</td>
      <td>4030432</td>
      <td>24182592</td>
      <td>19187003</td>
      <td>959350</td>
      <td>20146353</td>
      <td>21506406</td>
      <td>4301281</td>
      <td>25807687</td>
      <td>9291063</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pennsylvania</td>
      <td>14155505</td>
      <td>2831101</td>
      <td>16986605</td>
      <td>13476156</td>
      <td>673808</td>
      <td>14149964</td>
      <td>15103663</td>
      <td>3020733</td>
      <td>18124395</td>
      <td>6525642</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illinois</td>
      <td>13899434</td>
      <td>2779887</td>
      <td>16679321</td>
      <td>13232290</td>
      <td>661615</td>
      <td>13893905</td>
      <td>14830251</td>
      <td>2966050</td>
      <td>17796302</td>
      <td>6407552</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Ohio</td>
      <td>12800802</td>
      <td>2560160</td>
      <td>15360963</td>
      <td>12186021</td>
      <td>609301</td>
      <td>12795322</td>
      <td>13657222</td>
      <td>2731444</td>
      <td>16388666</td>
      <td>5900905</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Michigan</td>
      <td>11247753</td>
      <td>2249551</td>
      <td>13497303</td>
      <td>10706992</td>
      <td>535350</td>
      <td>11242341</td>
      <td>11999001</td>
      <td>2399800</td>
      <td>14398801</td>
      <td>5184701</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Carolina</td>
      <td>10897295</td>
      <td>2179459</td>
      <td>13076753</td>
      <td>10373237</td>
      <td>518662</td>
      <td>10891899</td>
      <td>11624810</td>
      <td>2324962</td>
      <td>13949772</td>
      <td>5023083</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Georgia</td>
      <td>10826464</td>
      <td>2165293</td>
      <td>12991757</td>
      <td>10305783</td>
      <td>515289</td>
      <td>10821072</td>
      <td>11549183</td>
      <td>2309837</td>
      <td>13859020</td>
      <td>4990419</td>
    </tr>
  </tbody>
</table>
</div>



Wanted to make the totals a bit easier to read, so I added a column that rounded the figures to millions with two decimal places:


```python
all_three['total_as_millions'] = all_three.apply(lambda row: '$' + str(round(row['total_match']/1000000,2)) + 'MM', axis=1)
```

The result: 


```python
all_three.head()
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
      <th>state</th>
      <th>fed_share_cares</th>
      <th>state_share_cares</th>
      <th>total</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
      <th>total_match</th>
      <th>total_as_millions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6473611</td>
      <td>1294722</td>
      <td>7768334</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
      <td>2983054</td>
      <td>$2.98MM</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>1350000</td>
      <td>$1.35MM</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7842119</td>
      <td>1568424</td>
      <td>9410542</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
      <td>3614156</td>
      <td>$3.61MM</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4703886</td>
      <td>940777</td>
      <td>5644663</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
      <td>2166926</td>
      <td>$2.17MM</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36293345</td>
      <td>7258669</td>
      <td>43552014</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
      <td>16734744</td>
      <td>$16.73MM</td>
    </tr>
  </tbody>
</table>
</div>



Now we can sort on those:


```python
all_three.sort_values('total_as_millions', ascending=False)
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
      <th>state</th>
      <th>fed_share_cares</th>
      <th>state_share_cares</th>
      <th>total</th>
      <th>fed_share_18</th>
      <th>sm_18</th>
      <th>total_18</th>
      <th>fed_share_20</th>
      <th>sm_20</th>
      <th>total_20</th>
      <th>total_match</th>
      <th>total_as_millions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>32</th>
      <td>New York</td>
      <td>20463651</td>
      <td>4092730</td>
      <td>24556381</td>
      <td>19483647</td>
      <td>974182</td>
      <td>20457829</td>
      <td>21838990</td>
      <td>4367798</td>
      <td>26206788</td>
      <td>9434710</td>
      <td>$9.43MM</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Florida</td>
      <td>20152160</td>
      <td>4030432</td>
      <td>24182592</td>
      <td>19187003</td>
      <td>959350</td>
      <td>20146353</td>
      <td>21506406</td>
      <td>4301281</td>
      <td>25807687</td>
      <td>9291063</td>
      <td>$9.29MM</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pennsylvania</td>
      <td>14155505</td>
      <td>2831101</td>
      <td>16986605</td>
      <td>13476156</td>
      <td>673808</td>
      <td>14149964</td>
      <td>15103663</td>
      <td>3020733</td>
      <td>18124395</td>
      <td>6525642</td>
      <td>$6.53MM</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illinois</td>
      <td>13899434</td>
      <td>2779887</td>
      <td>16679321</td>
      <td>13232290</td>
      <td>661615</td>
      <td>13893905</td>
      <td>14830251</td>
      <td>2966050</td>
      <td>17796302</td>
      <td>6407552</td>
      <td>$6.41MM</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Ohio</td>
      <td>12800802</td>
      <td>2560160</td>
      <td>15360963</td>
      <td>12186021</td>
      <td>609301</td>
      <td>12795322</td>
      <td>13657222</td>
      <td>2731444</td>
      <td>16388666</td>
      <td>5900905</td>
      <td>$5.9MM</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Michigan</td>
      <td>11247753</td>
      <td>2249551</td>
      <td>13497303</td>
      <td>10706992</td>
      <td>535350</td>
      <td>11242341</td>
      <td>11999001</td>
      <td>2399800</td>
      <td>14398801</td>
      <td>5184701</td>
      <td>$5.18MM</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Carolina</td>
      <td>10897295</td>
      <td>2179459</td>
      <td>13076753</td>
      <td>10373237</td>
      <td>518662</td>
      <td>10891899</td>
      <td>11624810</td>
      <td>2324962</td>
      <td>13949772</td>
      <td>5023083</td>
      <td>$5.02MM</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Georgia</td>
      <td>10826464</td>
      <td>2165293</td>
      <td>12991757</td>
      <td>10305783</td>
      <td>515289</td>
      <td>10821072</td>
      <td>11549183</td>
      <td>2309837</td>
      <td>13859020</td>
      <td>4990419</td>
      <td>$4.99MM</td>
    </tr>
    <tr>
      <th>30</th>
      <td>New Jersey</td>
      <td>10250690</td>
      <td>2050138</td>
      <td>12300828</td>
      <td>9757450</td>
      <td>487873</td>
      <td>10245323</td>
      <td>10934419</td>
      <td>2186884</td>
      <td>13121302</td>
      <td>4724895</td>
      <td>$4.72MM</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Virginia</td>
      <td>9540102</td>
      <td>1908020</td>
      <td>11448123</td>
      <td>9080731</td>
      <td>454037</td>
      <td>9534767</td>
      <td>10175711</td>
      <td>2035142</td>
      <td>12210853</td>
      <td>4397199</td>
      <td>$4.4MM</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Washington</td>
      <td>8308437</td>
      <td>1661687</td>
      <td>9970124</td>
      <td>7907768</td>
      <td>395388</td>
      <td>8303156</td>
      <td>8860638</td>
      <td>1772128</td>
      <td>10632765</td>
      <td>3829203</td>
      <td>$3.83MM</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Massachusetts</td>
      <td>8290676</td>
      <td>1658135</td>
      <td>9948811</td>
      <td>7890854</td>
      <td>394543</td>
      <td>8285396</td>
      <td>8841674</td>
      <td>1768335</td>
      <td>10610009</td>
      <td>3821013</td>
      <td>$3.82MM</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Indiana</td>
      <td>7980108</td>
      <td>1596022</td>
      <td>9576130</td>
      <td>7595088</td>
      <td>379754</td>
      <td>7974842</td>
      <td>8510075</td>
      <td>1702015</td>
      <td>10212090</td>
      <td>3677791</td>
      <td>$3.68MM</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Tennessee</td>
      <td>7948953</td>
      <td>1589791</td>
      <td>9538744</td>
      <td>7565418</td>
      <td>378271</td>
      <td>7943689</td>
      <td>8476810</td>
      <td>1695362</td>
      <td>10172173</td>
      <td>3663424</td>
      <td>$3.66MM</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7842119</td>
      <td>1568424</td>
      <td>9410542</td>
      <td>7463675</td>
      <td>373184</td>
      <td>7836859</td>
      <td>8362741</td>
      <td>1672548</td>
      <td>10035289</td>
      <td>3614156</td>
      <td>$3.61MM</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Missouri</td>
      <td>7597405</td>
      <td>1519481</td>
      <td>9116886</td>
      <td>7230625</td>
      <td>361531</td>
      <td>7592156</td>
      <td>8101455</td>
      <td>1620291</td>
      <td>9721746</td>
      <td>3501303</td>
      <td>$3.5MM</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Maryland</td>
      <td>7422125</td>
      <td>1484425</td>
      <td>8906550</td>
      <td>7063699</td>
      <td>353185</td>
      <td>7416884</td>
      <td>7914306</td>
      <td>1582861</td>
      <td>9497167</td>
      <td>3420471</td>
      <td>$3.42MM</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Wisconsin</td>
      <td>7332471</td>
      <td>1466494</td>
      <td>8798965</td>
      <td>6978318</td>
      <td>348916</td>
      <td>7327234</td>
      <td>7818581</td>
      <td>1563716</td>
      <td>9382297</td>
      <td>3379126</td>
      <td>$3.38MM</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Minnesota</td>
      <td>6930610</td>
      <td>1386122</td>
      <td>8316732</td>
      <td>6595610</td>
      <td>329781</td>
      <td>6925391</td>
      <td>7389506</td>
      <td>1477901</td>
      <td>8867407</td>
      <td>3193804</td>
      <td>$3.19MM</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Colorado</td>
      <td>6665335</td>
      <td>1333067</td>
      <td>7998402</td>
      <td>6342979</td>
      <td>317149</td>
      <td>6660128</td>
      <td>7106267</td>
      <td>1421253</td>
      <td>8527521</td>
      <td>3071469</td>
      <td>$3.07MM</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6473611</td>
      <td>1294722</td>
      <td>7768334</td>
      <td>6160393</td>
      <td>308020</td>
      <td>6468413</td>
      <td>6901560</td>
      <td>1380312</td>
      <td>8281872</td>
      <td>2983054</td>
      <td>$2.98MM</td>
    </tr>
    <tr>
      <th>41</th>
      <td>South Carolina</td>
      <td>6348027</td>
      <td>1269605</td>
      <td>7617633</td>
      <td>6040794</td>
      <td>302040</td>
      <td>6342834</td>
      <td>6767471</td>
      <td>1353494</td>
      <td>8120966</td>
      <td>2925139</td>
      <td>$2.93MM</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Kentucky</td>
      <td>6067275</td>
      <td>1213455</td>
      <td>7280730</td>
      <td>5773423</td>
      <td>288671</td>
      <td>6062094</td>
      <td>6467707</td>
      <td>1293541</td>
      <td>7761248</td>
      <td>2795667</td>
      <td>$2.8MM</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Louisiana</td>
      <td>6189147</td>
      <td>1237829</td>
      <td>7426977</td>
      <td>5889487</td>
      <td>294474</td>
      <td>6183961</td>
      <td>6597832</td>
      <td>1319566</td>
      <td>7917398</td>
      <td>2851869</td>
      <td>$2.85MM</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Oregon</td>
      <td>5636292</td>
      <td>1127258</td>
      <td>6763550</td>
      <td>5362981</td>
      <td>268149</td>
      <td>5631130</td>
      <td>6007538</td>
      <td>1201508</td>
      <td>7209045</td>
      <td>2596915</td>
      <td>$2.6MM</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Oklahoma</td>
      <td>5460972</td>
      <td>1092194</td>
      <td>6553166</td>
      <td>5196017</td>
      <td>259801</td>
      <td>5455818</td>
      <td>5820345</td>
      <td>1164069</td>
      <td>6984414</td>
      <td>2516064</td>
      <td>$2.52MM</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Connecticut</td>
      <td>5381732</td>
      <td>1076346</td>
      <td>6458079</td>
      <td>5120554</td>
      <td>256028</td>
      <td>5376582</td>
      <td>5735740</td>
      <td>1147148</td>
      <td>6882887</td>
      <td>2479522</td>
      <td>$2.48MM</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Puerto Rico</td>
      <td>5461964</td>
      <td>1092393</td>
      <td>6554357</td>
      <td>3676962</td>
      <td>183848</td>
      <td>3860810</td>
      <td>5821405</td>
      <td>1164281</td>
      <td>6985686</td>
      <td>2440522</td>
      <td>$2.44MM</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Iowa</td>
      <td>4843615</td>
      <td>968723</td>
      <td>5812338</td>
      <td>4608084</td>
      <td>230404</td>
      <td>4838488</td>
      <td>5161182</td>
      <td>1032236</td>
      <td>6193418</td>
      <td>2231363</td>
      <td>$2.23MM</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4703886</td>
      <td>940777</td>
      <td>5644663</td>
      <td>4475015</td>
      <td>223751</td>
      <td>4698766</td>
      <td>5011991</td>
      <td>1002398</td>
      <td>6014389</td>
      <td>2166926</td>
      <td>$2.17MM</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Mississippi</td>
      <td>4712839</td>
      <td>942568</td>
      <td>5655407</td>
      <td>4483541</td>
      <td>224177</td>
      <td>4707718</td>
      <td>5021549</td>
      <td>1004310</td>
      <td>6025859</td>
      <td>2171055</td>
      <td>$2.17MM</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Kansas</td>
      <td>4607890</td>
      <td>921578</td>
      <td>5529469</td>
      <td>4383595</td>
      <td>219180</td>
      <td>4602774</td>
      <td>4909494</td>
      <td>981899</td>
      <td>5891393</td>
      <td>2122657</td>
      <td>$2.12MM</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Nevada</td>
      <td>4496720</td>
      <td>899344</td>
      <td>5396064</td>
      <td>4277723</td>
      <td>213886</td>
      <td>4491609</td>
      <td>4790795</td>
      <td>958159</td>
      <td>5748954</td>
      <td>2071389</td>
      <td>$2.07MM</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36293345</td>
      <td>7258669</td>
      <td>43552014</td>
      <td>34558874</td>
      <td>1727944</td>
      <td>36286818</td>
      <td>38740655</td>
      <td>7748131</td>
      <td>46488786</td>
      <td>16734744</td>
      <td>$16.73MM</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Texas</td>
      <td>24421231</td>
      <td>4884246</td>
      <td>29305478</td>
      <td>23252604</td>
      <td>1162630</td>
      <td>24415234</td>
      <td>26064574</td>
      <td>5212915</td>
      <td>31277489</td>
      <td>11259791</td>
      <td>$11.26MM</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Utah</td>
      <td>4321708</td>
      <td>864342</td>
      <td>5186049</td>
      <td>4111052</td>
      <td>205553</td>
      <td>4316605</td>
      <td>4603931</td>
      <td>920786</td>
      <td>5524718</td>
      <td>1990681</td>
      <td>$1.99MM</td>
    </tr>
    <tr>
      <th>31</th>
      <td>New Mexico</td>
      <td>3889527</td>
      <td>777905</td>
      <td>4667433</td>
      <td>3699470</td>
      <td>184973</td>
      <td>3884443</td>
      <td>4142484</td>
      <td>828497</td>
      <td>4970981</td>
      <td>1791375</td>
      <td>$1.79MM</td>
    </tr>
    <tr>
      <th>49</th>
      <td>West Virginia</td>
      <td>3797620</td>
      <td>759524</td>
      <td>4557144</td>
      <td>3611943</td>
      <td>180597</td>
      <td>3792540</td>
      <td>4044353</td>
      <td>808871</td>
      <td>4853223</td>
      <td>1748992</td>
      <td>$1.75MM</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Nebraska</td>
      <td>3676857</td>
      <td>735371</td>
      <td>4412229</td>
      <td>3496936</td>
      <td>174847</td>
      <td>3671783</td>
      <td>3915413</td>
      <td>783083</td>
      <td>4698495</td>
      <td>1693301</td>
      <td>$1.69MM</td>
    </tr>
    <tr>
      <th>29</th>
      <td>New Hampshire</td>
      <td>3262422</td>
      <td>652484</td>
      <td>3914906</td>
      <td>3102253</td>
      <td>155113</td>
      <td>3257366</td>
      <td>3472912</td>
      <td>694582</td>
      <td>4167494</td>
      <td>1502179</td>
      <td>$1.5MM</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Idaho</td>
      <td>3396453</td>
      <td>679291</td>
      <td>4075744</td>
      <td>3229896</td>
      <td>161495</td>
      <td>3391391</td>
      <td>3616020</td>
      <td>723204</td>
      <td>4339223</td>
      <td>1563990</td>
      <td>$1.56MM</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Hawaii</td>
      <td>3295842</td>
      <td>659168</td>
      <td>3955010</td>
      <td>3134080</td>
      <td>156704</td>
      <td>3290784</td>
      <td>3508595</td>
      <td>701719</td>
      <td>4210314</td>
      <td>1517591</td>
      <td>$1.52MM</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Maine</td>
      <td>3292585</td>
      <td>658517</td>
      <td>3951102</td>
      <td>3130979</td>
      <td>156549</td>
      <td>3287528</td>
      <td>3505118</td>
      <td>701024</td>
      <td>4206142</td>
      <td>1516090</td>
      <td>$1.52MM</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Rhode Island</td>
      <td>3016343</td>
      <td>603269</td>
      <td>3619611</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3210168</td>
      <td>642034</td>
      <td>3852202</td>
      <td>1395303</td>
      <td>$1.4MM</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Montana</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3127978</td>
      <td>625596</td>
      <td>3753573</td>
      <td>1375596</td>
      <td>$1.38MM</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3031481</td>
      <td>606296</td>
      <td>3637777</td>
      <td>1356296</td>
      <td>$1.36MM</td>
    </tr>
    <tr>
      <th>42</th>
      <td>South Dakota</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>1350000</td>
      <td>$1.35MM</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>1350000</td>
      <td>$1.35MM</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Vermont</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>1350000</td>
      <td>$1.35MM</td>
    </tr>
    <tr>
      <th>8</th>
      <td>District of Columbia</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>1350000</td>
      <td>$1.35MM</td>
    </tr>
    <tr>
      <th>34</th>
      <td>North Dakota</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>3000000</td>
      <td>150000</td>
      <td>3150000</td>
      <td>3000000</td>
      <td>600000</td>
      <td>3600000</td>
      <td>1350000</td>
      <td>$1.35MM</td>
    </tr>
  </tbody>
</table>
</div>



Now let's just make a quick set with the top ten states with rounded figures:


```python
total_state_share = all_three[['state', 'total_as_millions']]
```


```python
total_state_share.sort_values('total_as_millions', ascending=False).head(10)
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
      <th>state</th>
      <th>total_as_millions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>32</th>
      <td>New York</td>
      <td>$9.43MM</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Florida</td>
      <td>$9.29MM</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pennsylvania</td>
      <td>$6.53MM</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illinois</td>
      <td>$6.41MM</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Ohio</td>
      <td>$5.9MM</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Michigan</td>
      <td>$5.18MM</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Carolina</td>
      <td>$5.02MM</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Georgia</td>
      <td>$4.99MM</td>
    </tr>
    <tr>
      <th>30</th>
      <td>New Jersey</td>
      <td>$4.72MM</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Virginia</td>
      <td>$4.4MM</td>
    </tr>
  </tbody>
</table>
</div>


