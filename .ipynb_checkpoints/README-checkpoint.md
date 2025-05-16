##  Dataset Overview

This dataset contains food inspection records from restaurants and other food establishments in Chicago, maintained by the Chicago Department of Public Health’s Food Protection Program. Inspections span from **January 1, 2010 to the present**, and include information such as business name, facility type, inspection type and result, violations recorded, and location.

Inspections are performed using a standardized procedure, and results are entered into a database and reviewed by a State of Illinois Licensed Environmental Health Practitioner (LEHP).


##  Data Dictionary (Post-Cleaning)

- **inspection_id**: Unique identifier for each inspection
- **dba_name**: Business name the establishment operates under
- **aka_name**: Alternate name or brand/franchise name
- **license_#**: City of Chicago business license number (stored as string)
- **facility_type**: Type of facility (e.g., Restaurant, Grocery Store)
- **risk**: Health risk classification; Risk 1 (High), Risk 2 (Medium), Risk 3 (Low), or Unknown
- **address**: Street address of the facility
- **city**: City in which the facility is located (cleaned and standardized)
- **state**: State abbreviation (e.g., IL)
- **zip**: 5-digit ZIP code (stored as string)
- **inspection_date**: Date of the inspection (converted to datetime)
- **inspection_type**: Type of inspection conducted (e.g., Canvass, Complaint)
- **results**: Outcome of the inspection (e.g., Pass, Fail, Out of Business)
- **violations**: Free-text list of violations noted during the inspection (can be null)
- **latitude** / **longitude** / **location**: Geographic coordinates (retained for mapping)
- **has_violations**: Boolean flag indicating whether any violations were recorded (`True` if `violations` is not null, else `False`)

---

##  Important Metadata Notes

- **Change in Violation Definitions:**  
  The format and classification of violations changed on **July 1, 2018**. Be cautious when comparing violations before and after this date.  
  *Source: https://data.cityofchicago.org/Health-Human-Services/Food-Inspections-7-1-2018-Present/qizy-d2wf/about_data


- **Original Column Descriptions:**  
  Available at: https://data.cityofchicago.org/api/assets/BAD5301B-681A-4202-9D25-51B2CAE672FF

---

##  Data Cleaning Summary

The following cleaning steps were performed to prepare the dataset for analysis:

- **Column Formatting**:
  - Renamed all column names to lowercase with underscores for consistency
  - Converted `inspection_date` from object to proper datetime format
  - Converted `license_#` and `zip` to string and cleaned any stringified nulls

- **Missing Value Handling**:
  - Filled missing `aka_name` values using `dba_name`
  - Filled missing `facility_type` based on known `aka_name` values; remaining nulls filled as `'Unknown'`
  - Imputed `risk` values based on dominant risk per `facility_type`; remaining nulls filled as `'Unknown Risk'`
  - Cleaned and standardized `city` (e.g., fixing capitalization, typos) and filled missing values using ZIP code when available
  - Filled missing `state` values based on known city (`CHICAGO`) or ZIP code (`60642`)
  - Dropped one row with missing `inspection_type`, `results`, and `violations` (incomplete record)

- **New Columns Added**:
  - Created `has_violations`, a boolean column indicating whether a violation was recorded during the inspection

- **Geographic Data**:
  - Retained `latitude`, `longitude`, and `location` columns for potential mapping, despite some missing values

- **Final Output**:
  - Cleaned dataset saved as `cleaned_chicago_food_inspections_v2.csv`


---

## Questions
- **Which areas in Chicago may need food reform?**:
  - Can we visualize heat zones on the map of Chicago that show high failure rates or frequent violations?
- **Are there any patterns in inspection results or violations by facility type**:
  - Do certain facility types consistently perform better or worse?
- **How did facilities adapt to the 2018 change in insepction definitions**?:
  - Did establishments maintain consistent inspection results before and after the rule change?
- **Are complaint based inspections more likely to result in failing inspection**?:
  - Does the inspection type correlate with failure rates?
- **Are specific business chains and franchises more likely to fail inspection**?:
  - Using 'dba_name' values can we identify patterns amongst large brands?
 

---

# 1. **Which areas in Chicago may need food reform?**
## How I mapped Chicago
 - I google searched github.com Illinois zip code GeoJSON
 - I found this repository [https://github.com/OpenDataDE/State-zip-code-GeoJSON]
 - Inside that repository I found the Illinois file I needed (https://github.com/OpenDataDE/State-zip-code-GeoJSON/blob/master/il_illinois_zip_codes_geo.min.json)
 - I saved the **'RAW'** URL
- **Mapping Results**:
## Mapping Results
- Most ZIP codes in Chicago show higher food inspection **pass rates**.
- Only one ZIP code — **60827** — has a notably higher **fail rate**:
  - **Fail Rate**: 49%
  - **Pass Rate**: 40%
- This suggests that ZIP code 60827 may be an area of concern for food safety reform efforts.

# 2. **Are there any patterns in inspection results or violations by facility type**
## Note on facility types
 - "Unknown" facility types were excluded from my analysis. Investigation results showed a large portion of these businesses were reported out of business. Keeping them would distort fail rates and misrepresent active businesses. 

- Facilities serving vulnerable groups such as senior care, shelters, daycares, and schools displayed higher inspection fail rates post the covid-19 pandemic. Traditional restaurants performed better, likley reflecting adaptions to stricter hygeine practices due to the covid-19 pandemic. Facilities handling live poultry exhibited the highest inspection fail rate among all groups, suggesting a need for increased oversight and stricter food safety protocols.

# 3. **How did facilities adapt to the 2018 change in insepction definitions**?
- Facilities serving vulnerable groups of people such as senior and medical care, schools, and shelters, showed some of the highest increases in inspection fail rates after 2018 suggesting greater compliance challenges.

- Facilities handling live poultry displayed a 8.7% increase in failure rates after 2018. This is a high risk food category and needs enhanced oversight and regulation.

- Daycares showed an alarming spike in inspection failures at a 14.2% increase. This is a high risk enviornment that needs enhanced oversight and regulation

- Shelters showed the largest spike in inspection fail rates at a 14.9% increase. 

- Additionally rooftop facilities, mobile vendors, and wholesale facilities showed the most improved inspection outcomes.

- Many businesses labeled "Unknown" appear to be active restaurants or food service establishments based off their names. However, without confirmed metadata I will not reclassify them and maintain the "Unknown" label to avoid a potential bias 

# 4. **Are complaint based inspections more likely to result in failing**? 
- Null Hypothesis: There is no association between complaint based inspections and inspection outcomes. The likelihood of failing an expection is the same whether or not the inspection was triggered by a complaint.

- Alternative Hypothesis: There is an association between complaint based inspections and outcomes. The likelihood of failing an inspection is increased when driven by a complaint.

    - A chi-squared of independence test was performed and concluded that the difference in failure rates between complaint driven (25.2%) and non complaint driven inspections (22%) is statistically significant and highly unlikely to be due to random chance.

- My p-value is 0.000 so I will reject the null hypothesis, and support the claim that complaint driven inspections do correlate with higher failure rates.

# 5. **Are specific business chains and franchises more likely to fail inspection**?:
    - I explored whether some businesses consistently fail but found that most entries appear to reflect individual locations. 
    - Without a reliable chain ID I can not draw confident conclusions about about business level trends
    - Popeye's appears 2 times within the top 50 but appears to represent 2 different locations and not the franchise as a whole.

## Limitations
- Facility_Type accuracy: Some establishments had "Unknown" facility types, limiting my ability to categorize them. While many appear to be restaurants, assumptions were avoided preserve integrity.

- Chain Identification: The "dba_name" column represents individual locations. Without a standardized chain ID, franchise level patterns could not be confidently assessed.

- Geographic Boundaries: Choropleth map was based on zip codes, not neighborhoods.


## Final Thoughts
- Facilities serving vulnerable groups of people such has schools, shelters, day cares, and medical care showed some of the highest increases in inspection fail rates after 2018, highlighting a need for stronger oversight.

- Complaint based inspections are statistically more likely to result in inspection failure as confirmed by the Chi-Squared Test that accounts for the difference in group sizes, which means the comparison is adjusted for how many inspections occurred in each group and is unlikely due to random chance.  

- Most inspections conducted do not stem from complaints, and most facilities pass but outliers exist and often clutter in certain zip codes and facility types.

- Zip codes 60827, 60617, and 60628 displayed the highest inspection fail rates. However, only zip code 60827 had a higher failure rating than passing rate. 
