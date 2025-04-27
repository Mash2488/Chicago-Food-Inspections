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
  - Using 'aka_name' values can we identify patterns amongst large brands?
 

---

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






