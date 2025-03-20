# Median Household Income Trends in Texas Counties (ACS 5-Year Estimates, 2010-2023)

>**Note: Before running the code, install the necessary packages (if you haven’t already)

```python
pip install pandas matplotlib requests census us
```

```python
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.ticker as mtick
import requests
from census import Census
from us import states
```

# Set Up Census Data API Key


```python
api_key = "c8fe079555b37f7ba964e5d3d000e4ddd242d9ba"
```

# Check for Specific Table in Multiple Years
## Define years and the target table (median household income)


```python
years = list(range(2011, 2023))
table_name = "B19013A_001E"
table_check = []

for year in years:
    url = f"https://api.census.gov/data/{year}/acs/acs5/variables.json"
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        # The variables are in the "variables" key of the JSON response
        variables = data.get("variables", {})
        exists = table_name in variables
        table_check.append(exists)
    else:
        # If there is an error, we mark the table as not present
        table_check.append(False)
```

## Variable check


```python
table_name = "B19013A_001E"

```

## Confirm the Variable Name


```python
for year in years:
    url = f"https://api.census.gov/data/{year}/acs/acs5/variables.json"
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        variables = data.get("variables", {})
        # Print the first few keys to inspect
        print(f"Year {year} keys:", list(variables.keys())[:10])

```

    Year 2011 keys: ['for', 'in', 'ucgid', 'B99104_007E', 'B24022_060E', 'B11011_007E', 'B19001B_014E', 'B24032_049E', 'B11011_006E', 'B07007PR_019E']
    Year 2012 keys: ['for', 'in', 'ucgid', 'B99104_007E', 'B24022_060E', 'B11011_007E', 'B19001B_014E', 'B24032_049E', 'B11011_006E', 'B07007PR_019E']
    Year 2013 keys: ['for', 'in', 'ucgid', 'B99104_007E', 'B24022_060E', 'B11011_007E', 'B19001B_014E', 'B24032_049E', 'B11011_006E', 'B07007PR_019E']
    Year 2014 keys: ['for', 'in', 'ucgid', 'B99104_007E', 'B24022_060E', 'B11011_007E', 'B19001B_014E', 'B24032_049E', 'B11011_006E', 'B07007PR_019E']
    Year 2015 keys: ['for', 'in', 'ucgid', 'B99104_007E', 'B24022_060E', 'B11011_007E', 'B19001B_014E', 'B24032_049E', 'B11011_006E', 'B07007PR_019E']
    Year 2016 keys: ['for', 'in', 'ucgid', 'B99104_007E', 'B24022_060E', 'B11011_007E', 'B19001B_014E', 'B24032_049E', 'B11011_006E', 'B07007PR_019E']
    Year 2017 keys: ['for', 'in', 'ucgid', 'B24022_060E', 'B19001B_014E', 'B07007PR_019E', 'B19101A_004E', 'B24022_061E', 'B19001B_013E', 'B07007PR_018E']
    Year 2018 keys: ['for', 'in', 'ucgid', 'B24022_060E', 'B19001B_014E', 'B07007PR_019E', 'B19101A_004E', 'B24022_061E', 'B19001B_013E', 'B07007PR_018E']
    Year 2019 keys: ['for', 'in', 'ucgid', 'B24022_060E', 'B19001B_014E', 'B07007PR_019E', 'B19101A_004E', 'B24022_061E', 'B19001B_013E', 'B07007PR_018E']
    Year 2020 keys: ['for', 'in', 'ucgid', 'B24022_060E', 'B19001B_014E', 'B07007PR_019E', 'B19101A_004E', 'B24022_061E', 'B19001B_013E', 'B07007PR_018E']
    Year 2021 keys: ['for', 'in', 'ucgid', 'B24022_060E', 'B19001B_014E', 'B07007PR_019E', 'B19101A_004E', 'B24022_061E', 'B19001B_013E', 'B07007PR_018E']
    Year 2022 keys: ['for', 'in', 'ucgid', 'B24022_060E', 'B19001B_014E', 'B07007PR_019E', 'B19101A_004E', 'B24022_061E', 'B19001B_013E', 'B07007PR_018E']
    

# Create a DataFrame summarizing the results


```python
table_check_df = pd.DataFrame({
    'year': years,
    'table_exists': table_check
})
print("Table check across years:")
print(table_check_df)

if all(table_check):
    print(f"The table {table_name} is present in all years from 2011 to 2023.")
else:
    print(f"The table {table_name} is not present in all years from 2011 to 2023.")
```

    Table check across years:
        year  table_exists
    0   2011          True
    1   2012          True
    2   2013          True
    3   2014          True
    4   2015          True
    5   2016          True
    6   2017          True
    7   2018          True
    8   2019          True
    9   2020          True
    10  2021          True
    11  2022          True
    The table B19013A_001E is present in all years from 2011 to 2023.
    

# Load Variables for the ACS (5-Year) & Inspect


```python
url_latest = "https://api.census.gov/data/2023/acs/acs5/variables.json"
response_latest = requests.get(url_latest)
if response_latest.status_code == 200:
    variables_latest = response_latest.json().get("variables", {})
    # For example, print a few variable keys to inspect
    print("\nSample variables from 2023 ACS 5:")
    for key in list(variables_latest.keys())[:5]:
        print(key, variables_latest[key])
else:
    variables_latest = {}
    print("Failed to load latest ACS variables.")
```

    
    Sample variables from 2023 ACS 5:
    for {'label': "Census API FIPS 'for' clause", 'concept': 'Census API Geography Specification', 'predicateType': 'fips-for', 'group': 'N/A', 'limit': 0, 'predicateOnly': True}
    in {'label': "Census API FIPS 'in' clause", 'concept': 'Census API Geography Specification', 'predicateType': 'fips-in', 'group': 'N/A', 'limit': 0, 'predicateOnly': True}
    ucgid {'label': 'Uniform Census Geography Identifier clause', 'concept': 'Census API Geography Specification', 'predicateType': 'ucgid', 'group': 'N/A', 'limit': 0, 'predicateOnly': True, 'hasGeoCollectionSupport': True}
    B24022_060E {'label': 'Estimate!!Total:!!Female:!!Service occupations:!!Food preparation and serving related occupations', 'concept': 'Sex by Occupation and Median Earnings in the Past 12 Months (in 2023 Inflation-Adjusted Dollars) for the Full-Time, Year-Round Civilian Employed Population 16 Years and Over', 'predicateType': 'int', 'group': 'B24022', 'limit': 0, 'attributes': 'B24022_060EA,B24022_060M,B24022_060MA'}
    B19001B_014E {'label': 'Estimate!!Total:!!$100,000 to $124,999', 'concept': 'Household Income in the Past 12 Months (in 2023 Inflation-Adjusted Dollars) (Black or African American Alone Householder)', 'predicateType': 'int', 'group': 'B19001B', 'limit': 0, 'attributes': 'B19001B_014EA,B19001B_014M,B19001B_014MA'}
    

# Identify the Table Name for Median Household Income

## In our case, we directly set the variable (estimate) to use.


```python
medHHIncTX = table_name  # "B19013A_001E"
var_moe = "B19013A_001M"
```

> **Note:** The margin of error variable is typically the estimate variable + "M"

## Specify Counties and Years of Interest


```python
my_counties = ["Jones", "Taylor"]
```

> **Note:** The years list is already defined above (2011 to 2023)

# Request Data for Median Household Income Across Years

 >**Note:** For each year, we fetch NAME, the estimate (B19013A_001), and its margin of error (B19013A_001M)


```python
data_list = []
for yr in years:
    c = Census(api_key, year=yr)
    try:
        # Get data for all Texas counties (state FIPS "48")
        # The query returns a list of dictionaries with keys: "NAME", our variables, "state", and "county"
        result = c.acs5.get(("NAME", medHHIncTX, var_moe), {'for': 'county:*', 'in': 'state:48'})
        # Filter rows to include only counties that match those in our my_counties list.
        for item in result:
            # The NAME field is usually formatted as "CountyName County, Texas"
            if any(county_name in item['NAME'] for county_name in my_counties):
                item['year'] = yr  # add year information
                data_list.append(item)
    except Exception as e:
        print(f"Error fetching data for year {yr}: {e}")
```

# Convert the accumulated list into a DataFrame


```python
if data_list:
    texas_income = pd.DataFrame(data_list)
else:
    texas_income = pd.DataFrame()
print(texas_income)
print(texas_income.columns)
```

                        NAME  B19013A_001E  B19013A_001M state county  year
    0   Taylor County, Texas       45416.0        1714.0    48    441  2011
    1    Jones County, Texas       39622.0        3391.0    48    253  2011
    2   Taylor County, Texas       46491.0        1652.0    48    441  2012
    3    Jones County, Texas       41012.0        3701.0    48    253  2012
    4   Taylor County, Texas       46840.0        1427.0    48    441  2013
    5    Jones County, Texas       41386.0        3439.0    48    253  2013
    6   Taylor County, Texas       46931.0        1516.0    48    441  2014
    7    Jones County, Texas       43147.0        3951.0    48    253  2014
    8    Jones County, Texas       44653.0        5122.0    48    253  2015
    9   Taylor County, Texas       47737.0        1633.0    48    441  2015
    10   Jones County, Texas       48162.0        3348.0    48    253  2016
    11  Taylor County, Texas       50076.0        1754.0    48    441  2016
    12   Jones County, Texas       50374.0        4187.0    48    253  2017
    13  Taylor County, Texas       52594.0        1604.0    48    441  2017
    14  Taylor County, Texas       53940.0        1648.0    48    441  2018
    15   Jones County, Texas       50039.0        4968.0    48    253  2018
    16   Jones County, Texas       54597.0        5395.0    48    253  2019
    17  Taylor County, Texas       56597.0        2583.0    48    441  2019
    18   Jones County, Texas       55077.0        4962.0    48    253  2020
    19  Taylor County, Texas       58927.0        2639.0    48    441  2020
    20   Jones County, Texas       60011.0        4859.0    48    253  2021
    21  Taylor County, Texas       61767.0        2031.0    48    441  2021
    22   Jones County, Texas       65080.0        4401.0    48    253  2022
    23  Taylor County, Texas       65588.0        3511.0    48    441  2022
    Index(['NAME', 'B19013A_001E', 'B19013A_001M', 'state', 'county', 'year'], dtype='object')
    

# Rename Columns for Clarity & Convert Data Types


```python
if not texas_income.empty:
    texas_income = texas_income.rename(columns={
        "NAME": "TX_County",
        "B19013A_001E": "estimate",  # Use the correct key from your data
        "B19013A_001M": "MOE",         # Use the actual MOE key
        "county": "FIPS" 
    })
    # Convert the income and MOE values to numeric types
    texas_income['estimate'] = pd.to_numeric(texas_income['estimate'], errors='coerce')
    texas_income['MOE'] = pd.to_numeric(texas_income['MOE'], errors='coerce')

print("\nTexas Income Data:")
print(texas_income)
```

    
    Texas Income Data:
                   TX_County  estimate     MOE state FIPS  year
    0   Taylor County, Texas   45416.0  1714.0    48  441  2011
    1    Jones County, Texas   39622.0  3391.0    48  253  2011
    2   Taylor County, Texas   46491.0  1652.0    48  441  2012
    3    Jones County, Texas   41012.0  3701.0    48  253  2012
    4   Taylor County, Texas   46840.0  1427.0    48  441  2013
    5    Jones County, Texas   41386.0  3439.0    48  253  2013
    6   Taylor County, Texas   46931.0  1516.0    48  441  2014
    7    Jones County, Texas   43147.0  3951.0    48  253  2014
    8    Jones County, Texas   44653.0  5122.0    48  253  2015
    9   Taylor County, Texas   47737.0  1633.0    48  441  2015
    10   Jones County, Texas   48162.0  3348.0    48  253  2016
    11  Taylor County, Texas   50076.0  1754.0    48  441  2016
    12   Jones County, Texas   50374.0  4187.0    48  253  2017
    13  Taylor County, Texas   52594.0  1604.0    48  441  2017
    14  Taylor County, Texas   53940.0  1648.0    48  441  2018
    15   Jones County, Texas   50039.0  4968.0    48  253  2018
    16   Jones County, Texas   54597.0  5395.0    48  253  2019
    17  Taylor County, Texas   56597.0  2583.0    48  441  2019
    18   Jones County, Texas   55077.0  4962.0    48  253  2020
    19  Taylor County, Texas   58927.0  2639.0    48  441  2020
    20   Jones County, Texas   60011.0  4859.0    48  253  2021
    21  Taylor County, Texas   61767.0  2031.0    48  441  2021
    22   Jones County, Texas   65080.0  4401.0    48  253  2022
    23  Taylor County, Texas   65588.0  3511.0    48  441  2022
    

# Visualize the Data with Scatter and Error Bar Plots

## Check the DataFrame Structure


```python
print(texas_income.columns)
print(texas_income.head())
print("County column ndim:", texas_income['TX_County'].ndim)
print(sorted(texas_income['year'].unique()))
```

    Index(['TX_County', 'estimate', 'MOE', 'state', 'FIPS', 'year'], dtype='object')
                  TX_County  estimate     MOE state FIPS  year
    0  Taylor County, Texas   45416.0  1714.0    48  441  2011
    1   Jones County, Texas   39622.0  3391.0    48  253  2011
    2  Taylor County, Texas   46491.0  1652.0    48  441  2012
    3   Jones County, Texas   41012.0  3701.0    48  253  2012
    4  Taylor County, Texas   46840.0  1427.0    48  441  2013
    County column ndim: 1
    [2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018, 2019, 2020, 2021, 2022]
    

# Line Plot


```python
import matplotlib.pyplot as plt
import matplotlib.ticker as mtick

# Create figure and axis
fig, ax = plt.subplots(figsize=(8, 6))

# Group by TX-County so each county gets its own line
for county, group in texas_income.groupby('TX_County'):
    group = group.sort_values('year')
    ax.plot(group['year'], group['estimate'], marker='o', label=county)

# Set titles and axis labels
ax.set_title("Median Household Income Over Time")
ax.set_xlabel("Year")
ax.set_ylabel("Median Household Income")

# Format the y-axis as currency (e.g., $10,000)
ax.yaxis.set_major_formatter(mtick.StrMethodFormatter('${x:,.0f}'))

# Ensure the x-axis shows each year as an integer (assuming year is numeric)
years = sorted(texas_income['year'].unique())
ax.set_xticks(years)
ax.set_xticklabels(years)

ax.legend(title="County")
plt.tight_layout()
plt.show()
```

![output_31_0](https://github.com/user-attachments/assets/a9c2998b-76a5-46ea-83bc-a913a37230a1)

```python

```
