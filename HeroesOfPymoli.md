

```python
# import modules
import pandas as pd
import os
```


```python
# assign file path
json_path = os.path.join('HeroesOfPymoli','purchase_data.json')
#json_path = os.path.join('HeroesOfPymoli','purchase_data2.json')

# read json file
hp_df = pd.read_json(json_path)
hp_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Total number of players
# Players_Count = hp_df['SN'].count()
Players_Count = hp_df['SN'].nunique()
Players_df = pd.DataFrame({"Total Players" : [Players_Count]}, index=[0])
Players_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchasing Analysis (Total)
# Number of unique items
unique_items_count = hp_df["Item ID"].nunique()
# Number of purchases
total_purchase = len(hp_df["Price"])
# Total Revenue
total_revenue = sum(hp_df["Price"])
# total_rev = "$" + format(total_revenue,".2f")
total_rev = "$" + str(round(total_revenue,2))
# Average Purchase Price
# avg_purchase_price = "$" + format(total_revenue/total_purchase,".2f")
avg_purchase_price = round(total_revenue/total_purchase,2)
avg_purchase_price = "$" + str(avg_purchase_price)
# Purchasing Analysis (Total)
pa_table = pd.DataFrame({"Number of Unique Items": unique_items_count,
                         "Average Price": avg_purchase_price,
                         "Number of Purchases": total_purchase,
                         "Total Revenue": total_rev }, index=[0])
pa_table[["Number of Unique Items", "Average Price", "Number of Purchases","Total Revenue"]]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Gender Demographics
# Group By Gender and get count of each
gender_count = hp_df["Gender"].value_counts()
# Calculate Pecentage of Players by Gender
gender_percent = (gender_count/sum(gender_count)) * 100
gender_demographics = pd.DataFrame({"Total Count":gender_count,
                                    "Percentage of Players": gender_percent})
gender_demographics["Percentage of Players"] = pd.Series(["{0:.2f}".format(val) for val in gender_percent], index=gender_demographics.index)
gender_demographics
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>633</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.44</td>
      <td>136</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.41</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchasing Analysis(Gender)
# Group by Gender
pa_group = hp_df.groupby('Gender')
# Calculate no.of purchases and their value
purchase_count = pa_group["Price"].count()
purchase_value = pa_group["Price"].sum()
# get distinct player count for Normalized total
norm_gender_count = pa_group["SN"].nunique()
# Create a DataFrame
pa_df = pd.DataFrame({"Purchase Count": purchase_count,
                      "Average Purchase Price": (purchase_value/purchase_count),
                      "Total Purchase Value": purchase_value,
                      "Normalized Totals":(purchase_value/norm_gender_count)})
# format Amount Values
pa_df["Average Purchase Price"] = pd.Series(["${0:.2f}".format(val) for val in pa_df["Average Purchase Price"]], index=pa_df.index)
pa_df["Total Purchase Value"] = pd.Series(["${0:.2f}".format(val) for val in pa_df["Total Purchase Value"]], index=pa_df.index)
pa_df["Normalized Totals"] = pd.Series(["${0:.2f}".format(val) for val in pa_df["Normalized Totals"]], index=pa_df.index)

pa_df[["Purchase Count","Average Purchase Price", "Total Purchase Value","Normalized Totals"]]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Age Demographics
# Set bin values and their recpective labels
bins = [0,10,14,19,24,29,34,39,45]
group_names = ["<10","10-14","15-19","20-24","25-29","30-34","35-39","40+"]
# Create a new column and set the value based on the age bin
hp_df["Age Summary"] = pd.cut(hp_df["Age"], bins, labels=group_names)
# Group the data based on the bin values set up
age_group = hp_df.groupby("Age Summary")
# Calculate no. of purchases and their value
purchase_count = age_group["Price"].count()
purchase_value = age_group["Price"].sum()
# Calculate Average Purchase Price
avg_pur_price  = purchase_value/purchase_count
# Calculate Normalized Total
normalized_total = purchase_value/age_group["SN"].nunique()

# create a DataFrame
age_df = pd.DataFrame({"Purchase Count": purchase_count,
                       "Average Purchase Price": avg_pur_price,
                       "Total Purchase Value": purchase_value,
                       "Normalized Totals": normalized_total})
# format amount values
age_df["Total Purchase Value"] = pd.Series(["${0:.2f}".format(val) for val in age_df["Total Purchase Value"]], index=age_df.index)
age_df["Normalized Totals"] = pd.Series(["${0:.2f}".format(val) for val in age_df["Normalized Totals"]], index=age_df.index)
age_df["Average Purchase Price"] = pd.Series(["${0:.2f}".format(val) for val in age_df["Average Purchase Price"]],
                                   index=age_df.index)
# display data
age_df[["Purchase Count","Average Purchase Price", "Total Purchase Value","Normalized Totals"]]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Summary</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>32</td>
      <td>$3.02</td>
      <td>$96.62</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>31</td>
      <td>$2.70</td>
      <td>$83.79</td>
      <td>$4.19</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Top Spenders
# Group Data based on the players
sp_group = hp_df.groupby('SN')
# Calculate no. of purchases made by each player and their purchase value
purchase_count = sp_group["Price"].count()
purchase_value = sp_group["Price"].sum()
# Calculate Average Purchase Price for each player
avg_pur_price = purchase_value/purchase_count

# create a DataFrame
sp_df = pd.DataFrame({"Purchase Count": purchase_count,
                       "Average Purchase Price": avg_pur_price,
                       "Total Purchase Value": purchase_value })

# Sort DataFrame based on Total Purchase Value
sp_sorted_df = sp_df.sort_values(by="Total Purchase Value", ascending=False).head()
# Format Amount values
sp_sorted_df["Total Purchase Value"] = pd.Series(["${0:.2f}".format(val) for val in sp_sorted_df["Total Purchase Value"]], 
                                          index=sp_sorted_df.index)
sp_sorted_df["Average Purchase Price"] = pd.Series(["${0:.2f}".format(val) for val in sp_sorted_df["Average Purchase Price"]],
                                   index=sp_sorted_df.index)
'''                                          
sa_df['sort'] = sa_df["Total Purchase Value"].str.extract('(\d+)', expand=False).astype(int)
sa_df.sort_values('sort', inplace=True, ascending=False)
sa_df = sa_df.drop('sort', axis=1)                                         
'''
sp_sorted_df[["Purchase Count","Average Purchase Price", "Total Purchase Value"]]

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Most Popular Items
# Group the data based on the Item ID
pi_group = hp_df.groupby(["Item ID","Item Name"])

# Calculate No. of Purchases made for each Item and its Purchase Value
purchase_count = pi_group["Price"].count()
purchase_value = pi_group["Price"].sum()
# Calculate each Item Price
item_price = purchase_value/purchase_count

# Create a DataFrame
pi_df = pd.DataFrame({"Purchase Count": purchase_count,
                       "Item Price": item_price,
                       "Total Purchase Value": purchase_value })

# sort DataFrame based on Purchase Count
popular_item_df = pi_df.sort_values("Purchase Count",ascending=False).head()

# format Amount Values
popular_item_df["Total Purchase Value"] = pd.Series(["${0:.2f}".format(val) for val in popular_item_df["Total Purchase Value"]], 
                                          index=popular_item_df.index)
popular_item_df["Item Price"] = pd.Series(["${0:.2f}".format(val) for val in popular_item_df["Item Price"]],
                                   index=popular_item_df.index)
# show data
popular_item_df[["Purchase Count","Item Price", "Total Purchase Value"]]

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Most Profitable Items
# Use the above DataFrame but sort it based on Total Purchase Value 
pro_items_df = pi_df.sort_values(by="Total Purchase Value", ascending=False).head()
# format Amount Values
pro_items_df["Total Purchase Value"] = pd.Series(["${0:.2f}".format(val) for val in pro_items_df["Total Purchase Value"]], 
                                          index=pro_items_df.index)
pro_items_df["Item Price"] = pd.Series(["${0:.2f}".format(val) for val in pro_items_df["Item Price"]],
                                   index=pro_items_df.index)
# show data
pro_items_df[["Purchase Count","Item Price", "Total Purchase Value"]]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


