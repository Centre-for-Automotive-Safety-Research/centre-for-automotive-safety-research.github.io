# Public crash data analysis: Merging SA crash, vehicle and person data

> This tutorial will walk you through loading the previously saved crash, vehicle and person data, merge the datasets, then save the merged datasets. 

When working with crash data, information is often split across multiple files: 
- **Crash data**: Information about each crash (date, location, severity, etc.)
- **Vehicle (unit) data**: Information about vehicles involved in each crash
- **Person (casualty) data**: Information about people involved in each crash

To perform meaningful analyses, we need to link these datasets together. For example, if you want to study the types of vehicle involved in crashes with serious injuries, you need to connect crash details -> vehicles -> people. This is done using a process called **merging** in Python.



## Create a new Python script
Create a new file in the project folder and name it `2.Merge_SA_crash_data.py`. 

## Load the crash, vehicle and person 
We'll assume you have downloaded the crash data from [Data.SA](https://data.sa.gov.au/data/dataset/road-crash-data), and saved the raw data to a folder named: `Raw_SA_crash_data_2012_2023`, as outlined in [Downloading SA's public crash data](https://centre-for-automotive-safety-research.github.io/2025/06/11/public-crash-data-2-extracting-crash-data-sa.html).

```python
import pandas as pd
import os

# ---------------------------------------------------------
# Part 1: Define folder and file paths
# ---------------------------------------------------------
input_folder = "Raw_SA_crash_data_2012_2023"

crash_file   = os.path.join(input_folder, "2012_2023_crash.csv")
vehicle_file = os.path.join(input_folder, "2012_2023_vehicle.csv")
person_file  = os.path.join(input_folder, "2012_2023_person.csv")

# ---------------------------------------------------------
# Part 2: Load the CSVs into DataFrames
# ---------------------------------------------------------
crash_df   = pd.read_csv(crash_file, low_memory=False)
vehicle_df = pd.read_csv(vehicle_file, low_memory=False)
person_df  = pd.read_csv(person_file, low_memory=False)

print("Data loaded successfully:")
print(f"- Crash data:   {crash_df.shape}")
print(f"- Vehicle data: {vehicle_df.shape}")
print(f"- Person data:  {person_df.shape}")
```

## Merge vehicle data with crash data
This step combines the **vehicle dataset** with the **crash dataset** so that each vehicle record is linked to the corresponding crash details. 

The function *pd.merge* `(pd.merge(left_df, right_df, how="left", on=merge_columns)` works by performing a left join, which means:
- All rows from `left_df` are kept
- Corresponding data columns from `right_df` are added wherever `merge_columns` match between `left_df` and `right_df`

```python
# ---------------------------------------------------------
# Part 3: Merge vehicle data with crash data
# ---------------------------------------------------------
# Column to merge on
merge_columns_vehicle_crash = ["REPORT_ID"]

vehicle_crash_df = pd.merge(
    vehicle_df,
    crash_df,
    how="left",               # Use "left" join to keep all vehicle rows
    on=merge_columns_vehicle_crash
)

print(f"\nCrash + Vehicle merged: {vehicle_crash_df.shape}")
```


## Merge person data with vehicle + crash data
In the `person_df` dataset, the two columns we need for merging are "REPORT_ID" and "UND_UNIT_NUMBER". In the `vehicle_crash_df` dataset, the corresponding columns are "REPORT_ID" and "Unit No". 

Before merging, we rename these columns in both datasets so that the column names match. This makes the merge process straightforward and avoids errors.

```python
# ---------------------------------------------------------
# Part 4: Merge person data to the vehicle + crash data
# ---------------------------------------------------------
# Rename columns in vehicle and person datasets for an easier merge
vehicle_crash_df = vehicle_crash_df.rename(columns={
    "Unit No": "UNIT_NO"
})

person_df = person_df.rename(columns={
    "UND_UNIT_NUMBER": "UNIT_NO"
})

# Columns to merge on
merge_columns_person_vehicle_crash = ["CRASH_ID", "UNIT_NO"]

# Merge person data to crash + vehicle data
crash_vehicle_person_df = pd.merge(
    person_df, 
    vehicle_crash_df,       # previously merged vehicle + crash
    how="left",             # keep all rows from person_df
    on=merge_columns_person_vehicle_crash
)

print(f"Crash + Vehicle + Person merged: {crash_vehicle_person_df.shape}")
```

## Save merged data
After merging the datasets, we save the results for use in future analyses without re-running all the merges.

```python
# ---------------------------------------------------------
# Part 5: Save merged files back to the same folder
# ---------------------------------------------------------
crash_vehicle_file = os.path.join(input_folder, "2012_2023_crash_vehicle.csv")
crash_vehicle_person_file = os.path.join(input_folder, "2012_2023_crash_vehicle_person.csv")

vehicle_crash_df.to_csv(crash_vehicle_file, index=False)
crash_vehicle_person_df.to_csv(crash_vehicle_person_file, index=False)

print("\nMerged files saved successfully:")
print(f"- {crash_vehicle_file}")
print(f"- {crash_vehicle_person_file}\n")
```


