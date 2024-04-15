
# Geographic Variances in Police Shootings: Analyzing Root Causes (EDA)

This project analyses Fatal police shooting in US. The analysed dataset is retrieved from Fatal Force Database that is curated by Washington Post. Data cleaning and imputations are performed. Analysis goal is to identify the driving factors and related variables that impact the US of police deadly force. 
External datasets such as Fatal Encounters are used.

## Data
Various data sources are used for imputing and visualizing the dataset.
### Datasets
 - [Washington Post Fatal Force v2](https://github.com/washingtonpost/data-police-shootings?tab=readme-ov-file#fatal-force-database)
 - [Fatal Encounters](https://fatalencounters.org)
 - [FBI NICS Firearm Background Check Data](https://github.com/BuzzFeedNews/nics-firearm-background-checks)
 - US states sizes files retrieved from: 
    - https://en.wikipedia.org/wiki/List_of_U.S._states_and_territories_by_area
    - https://en.wikipedia.org/wiki/List_of_U.S._state_and_territory_abbreviations
 - US population: 2010-2024 by state:
    - https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html
    - https://www.census.gov/data/tables/time-series/demo/popest/2010s-state-total.html
 - [Firearm laws grade in different state](https://wisevoter.com/state-rankings/states-with-strictest-gun-laws/)

### Geojson files
 - [US states Geojson](https://github.com/washingtonpost/data-police-shootings?tab=readme-ov-file#fatal-force-database)
 - [US Counties Geojson](https://gist.github.com/sdwfrost/d1c73f91dd9d175998ed166eb216994a)

## Repository structure
Repository has a few directories:
 - <span style="color:green">**Data_sources**</span>: contain various data sources used for analysis.
 - <span style="color:green">**Data_preprocessing_full_files**</span>: contains full files with all imputations and retrieved results.
 - <span style="color:green">**Error_logs**</span>: contains csv files, with errors generated during imputation of location related details.
 - <span style="color:green">**GeoJson**</span>: contains geojson files used for creating visualizations.
 - <span style="color:green">**Folium maps**</span>: contains html format of maps generated during data cleaning and analysis.

Files:
 - <span style="color:green">**Feature_meaning.md**</span>: describes feature meaning and variables.
 - <span style="color:green">**location_details_imputation.ipynb**</span>: for location details imputation.
 - <span style="color:green">**victim_and_case_details_imputation.ipynb**</span>: for case details imputation and final cleaning.
 - <span style="color:green">**exploratory_data_analysis.ipynb**</span>: final file that presents the findings and visualizations.
 - <span style="color:green">**washington_post_v3_clean.csv**</span>: dataset file with location imputations for further analysis.
 - <span style="color:green">**washington_post_v4_clean.csv**</span>: dataset file with location imputations for final analysis.

## Google Colaboratory environment
Google colaboratory is recommended to view Folium interactive maps:
1. [Location details imputation](https://colab.research.google.com/drive/1IIPNWrxyMloyX7s8kJ_L2BGqcKPmBJpv?usp=sharing)
2. [Victim and case details imputation](https://colab.research.google.com/drive/1xaepRjkaCGwS-7dHJ1QB07luscBtAuHn?usp=sharing)
3. [EDA](https://colab.research.google.com/drive/1PxvkbZ1BNF8THPN7yFabweh5nhnWUefH?usp=sharing)


## Summary
<span style="color:green">Goal</span>: To identify what factors affect the use of deadly police force in US.

<span style="color:green">Narrative</span>: Analysis of the differences between US states is chosen since each state can have slightly different legislation.

<span style="color:green">Location data cleaning results</span>: missing 4731 county, 71 city, and 1034 of each latitude longitude and location precision values where possible imputed or determined as undetermined.
Function for location data imputation example:

```{python}
# Function to retrieve the city value using latitude and longitude
def get_city(latitudes, longitudes):
    """Function retrieves city name using latitude and longitude.
    Nominatim geolocator services are used."""
    geolocator = Nominatim(user_agent="DS_project")
    try:
        time.sleep(1)  # Rate limits of the service

        locate = geolocator.reverse((latitudes, longitudes),
                                    language="en", timeout=10)
        if locate:
            return locate.raw['address']["city"]
    except Exception as e:
        error_message = (f"Error getting city"
                         f" for: {latitudes, longitudes}: {e}")
        write_error_to_csv(error_message,
                           "/Users/agnekrupinskaite/PycharmProjects/"
                           "agkrupi-DWWP.4.1/Error_logs",
                           "error_log_get_city.csv")
    return None
```

<span style="color:green">Case and victim data cleaning results</span>: 1171 race, 1282 flee_status, 388 age, 352 name, 212 armed_with, 67 threat_type and 25 gender values where possible imputed from Fatal Encounters dataset or computationally or identified and "undetermined".
Function for victim details imputation example:

```{python}
# Function to retrieve names from fatal encounters dataset
def fill_missing_names(state, date, city, gender, age):
    """Fill missing names using matching state, date, city,
    gender and county. Fatal encounters dataset is used."""
    matching_row = fatal_encounters[
        (fatal_encounters["state"] == state)
        & (fatal_encounters["date"] == date)
        & (fatal_encounters["city"] == city)
        & (fatal_encounters["gender"] == gender)
        & (fatal_encounters["age"] == age)
    ]

    # Finding matching rows
    if not matching_row.empty:
        return matching_row["name"].iloc[0]
```

<span style="color:green">Exploratory data analysis main insights</span>:
1. Yearly count of deadly force usage by police in the US is growing during 2016-2023.
2. Highest amount of incident happen on the March, August and December months. December in the US is also a holiday season.
3. The impact of specific holidays was not identified during this analysis. results showed that 6, 11, and 15 days of the month are the most repeating days in the dataset. Overall highest incident count through the years have 5th, 3rd and 9th days of the month.
4. During the years of 2015-2024. The 5 states that had the most incidents related to police aggression were California, Texas, Florida, Arizona and Georgia. They make up 38.0 % of all incidents deadly force incidents in the US.
5. Most people involved in accidents in California are Hispanic. Most people involved in accidents in Texas are White. Black people are the most involved in accidents in Texas and Florida. Florida also has high incident count with withe people involved. This only shows the states that stand out with high overall incident and race related incident count are California, Texas and Florida. To understand this insight better, additional resource such as race distribution in different states population is needed.
6. Pearson and Spearman correlation show, that day and incident count in different states are not related.
7. Pearson and Spearman correlation show, month and incident count in different states are not related.

<span style="color:green">Narrowing the scope of the analysis: California, Texas and Florida</span>:
1. Pearson and Spearman correlation show, month and incident count in California, Teas and Florida are not related.
2. Female percentage involved in fatal shooting accidents in California, Texas and Florida is 5.0 %. This means that 95.0 % of accidents involve men. However, it is not clear if the dataset is biased towards men, or any behavioural differences more often lead to police usage of deadly force towards men.
3. More deadly force usage incidents in California, Texas and Florida happened when victims were not fleeing. More incidents overall happened when victims were not fleeing and threatening the police compared to the cases where victims were fleeing and threatening the police. However, the amount of cases that have undetermined flee status in California, Texas and Florida is 9.0 %. Efforts should be put to collect more details about deadly force cases to understand the police behaviour better. Flee status and threat type are highly correlated features according to accident counts by Pearson and Spearman coefficients.
4. 5 out of 10 counties that have the highest incident count are located in California. 2 out of 10 counties that have the highest incident count are located in Texas. More information is needed to understand if the counties have the biggest population, or are there any other deadly force incident driving forces.
5. Some counties do not have any deadly force incidents. These counties ar marked gray in the map. This creates question about how different counties and states or even databases track these incidents, are the counties without have better different approach to the use of deadly force, or the incidents are not tracked / tracked not well.
6. Population and area impact: Texas, California and Florida had growing population through 2010-2024, with California having the highest overall population through the 2017-2020. Also, California and Texas have overall the highest total area count. Could be that higher amount of incident are related to overall higher amount of population and therefore also higher amount of criminal activity.
7. Texas had the highest amount of incidents in 2020. California had the highest amount of incidents in 2015. Texas had the highest amount of incidents in 2019. Most incidents in California, Texas and Florida happened in 2020 May 2023 January and 2015 July. These months and years can be checked to evaluate if any changes in migration, employment or changes of legislation happened.
8. Firearm laws and Firearm checkups in the US: Firearm laws are the strictest in the California, the least strict in Texas and moderately strict in the Florida. Interesting that states with the biggest population have different firearm legislation but still comparably high incident count. Interestingly enough neither California, nor Florida or Texas are not the states that have the highest firearm checkup counts. The states that have the highest firearm checkup counts are Illinois with relatively high population and strict firearm laws and Kentucky that have only 193 and 163 incident counts comparing to California, which has 1304. The research gap in this case is firearm checkup can not be counted as firearm sale. Sales data would provide more important insights. Nevertheless, the question is are firearm checkups performed differently on the different states. Do fewer people in California want to buy a gun or the fewer checkups are performed and more illegal guns are being sold leading to the increased amounts of incident where police use deadly force.

<span style="color:green">Other insights</span>:
1. Different databases on the Interned even dedicated for the similar or same purpose have different data standardisation. This is data management gap, that makes it more complicated to combine knowledge from few sources such as Washington Post and Fatal Encounters database. This could be addressed in other researchers work.
2. Imputing race from surname computationally is a complicated task, since people have different amounts of surnames that can change in life, more over all. Ethnicolr library and its model census_ln did not show good performance on this dataset.
3. Gender can be imputed using libraries such as gender_guesser.
4. Geocoding is a powerful approach. Reversed part of geocoding might benefit from providing additional details, like if using city to retrieve coordinates, amount of false positive identifications can be reduced by adding state or country to the function.
5. Retrieval of missing names is also possible. More details are provided, more names can be retrieved. However, too high amount of details might make the retrieval strategy too narrow and reduce ability to impute the values.
6. GeoJson files is a great tool for plotting difference between different geographic locations. However, files or main data should always have matching formats.

## Requirements
The analysis is performed on Jupyter notebook. 

Python version 3.11.7 is used for analysis.
#### Data analysis and imputation
- [pandas](https://pandas.pydata.org)
- [numpy](https://numpy.org)
- [geopy](https://pypi.org/project/geopy/)
- [fuzzywuzzy](https://github.com/seatgeek/fuzzywuzzy)
- [ethnicolr](https://github.com/appeler/ethnicolr)
- [gender-guesser](https://pypi.org/project/gender-guesser/)

#### Statistics
- [scipy](https://scipy.org)
- [sklearn](https://scikit-learn.org/stable/)

#### Visualization
- [folium](https://pypi.org/project/folium/)
- [seaborn](https://seaborn.pydata.org)
- [matplotlib](https://matplotlib.org)

#### Code polishing
- [black-jupyter](https://pypi.org/project/jupyter-black/)
- [pycodestyle_magic](https://github.com/mattijn/pycodestyle_magic)

#### Others
- [tdqm](https://github.com/tqdm/tqdm)
- [csv](https://docs.python.org/3/library/csv.html)
- [os](https://docs.python.org/3/library/os.html)
- [time](https://docs.python.org/3/library/time.html)

## Acknowledgements

GeoJson files:
 - [MappingAPI repository: US states](https://github.com/PublicaMundi/MappingAPI)
 - [Simon Frost](https://gist.github.com/sdwfrost)

Amazing tool for readme:
 - [readme.so](https://readme.so/editor)

Well maintained databases about deadly force by police:
 - [Washington Post: Fatal Force database](https://www.washingtonpost.com/graphics/investigations/police-shootings-database/)
 - [Fatal Encounters](https://fatalencounters.org)

Firearm Background check data:
 - [FBI NICS Firearm Background Check Data](https://github.com/BuzzFeedNews/nics-firearm-background-checks)

## Ideas for future analysis
1. Analyze accidents variables when body camera was present and when not.
2. Give more attention to the states, where victims were hurt by accident.
3. Analyse fleeing situation impact on deadly force count more in depth: correlation coefficients of various groups, machine learning model.
4. Retrieve dataset about mental health issues in different states in US during different years and compare the results with the deadly force accident count.
5. Retrieve dataset about firearm purchase, license owners count. Not only checkups to have better look at comparison
6. Identify states that have the highest accident count but the lowest population and investigate potential issues.
7. Analyze employment and average income changed. This could be an important criteria during COVID19 pandemic years and could impact people behaviour and the same way the deadly force.
8. Include data about drug addictions in different states and see if there are any correlations between police deadly force accident and the drug addictions.
