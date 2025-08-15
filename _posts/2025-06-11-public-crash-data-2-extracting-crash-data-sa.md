# Public crash data analysis: Downloading SA's public crash data

Now that we’ve covered the basics of crash data structure (see [Public crash data analysis: Understanding typical crash data](https://centre-for-automotive-safety-research.github.io/2025/06/10/public-crash-data-1-understanding-crash-data.html) if you missed it), let’s explore where and how to access South Australia’s crash data.

### Exploring Data.SA crash data

The South Australian Government’s Department for Infrastructure and Transport (DIT) provides crash data in two main formats:

- **Data tables:** Detailed datasets for crashes, vehicles, and people involved. Some datasets cover 5-year periods, while others cover individual years. 
  Available here: [Crash data tables](https://data.sa.gov.au/data/dataset/road-crash-data).
  
- **Crash locations:** Geospatial data showing crash locations on maps.  
  Available here: [Crash location maps](https://data.sa.gov.au/data/dataset/road-crashes-in-sa)  

*Note: the geospatial data includes only the crash-level information - vehicle and person details are **not** included.*

This tutorial will walk you through downloading crash, vehicle, and person datasets for South Australian road crashes from [Data.SA](http://data.sa.gov.au), combining them into single files, and saving them to your computer.

---

## Downloading most recent (2019-2023) crash data from Data.SA

Let’s walk through how to download and load the most recent (2019-2023) crash, vehicle, and person data from the Data.SA's data tables using Python. 

Start by opening your Python editor and setting up your project. If you want to use VS Code and `uv`, follow the instructions in [Setting up a coding workspace](https://centre-for-automotive-safety-research.github.io/2025/06/09/setting-up-VS-Code.html).

Additionally, you’ll need to use a few libraries:

- `pandas`: a powerful data manipulation and analysis toolkit  
- `zipfile`: for handling ZIP files  
- `requests`: to download files from the web  
- `io`: for handling in-memory file operations
- `os`: for interacting with the operating system, e.g., creating folders and building file paths

If you do not have these libraries installed in your working environment, you will need to install them. If you are using `uv`, use the following code in your Python environment:

```bash
uv add pandas requests
```
*Note: `zipfile`, `io` and `os` are part of the Python standard library and do not require installation.*

You can choose to edit your existing `main.py` script, rename it, or start completely fresh. In this example, we’ll create a new script called `Download_SA_crash_data.py`. Add the code below to your chosen `.py` file to download crash data for the years 2019 to 2023:

```python
import pandas as pd 
import zipfile
import requests
import io
import os

# ---------------------------------------------------------
# Part 1: Download 2019-2023 data (all in one ZIP file)
# ---------------------------------------------------------
print("Downloading 2019-2023 crash, vehicle, and person data...")

zip_url_2019_2023 = "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/78d24425-6c14-426e-8895-d414c2a12521/download/2019-2023_data_sa_as_at_20240913.zip"
response = requests.get(zip_url_2019_2023)

# Open the ZIP file directly from memory (no need to save to disk)
with zipfile.ZipFile(io.BytesIO(response.content)) as z:
    print("Files found in ZIP:", z.namelist())  # optional
    raw_2019_2023_crash_data = pd.read_csv(z.open("2019-2023_DATA_SA_Crash.csv"), low_memory=False)
    raw_2019_2023_vehicle_data = pd.read_csv(z.open("2019-2023_DATA_SA_Units.csv"), low_memory=False)
    raw_2019_2023_person_data = pd.read_csv(z.open("2019-2023_DATA_SA_Casualty.csv"), low_memory=False)

print("Completed 2019-2023 download.\n")
```
*Note: DIT may update the datasets over time, so the download links could change. To get the correct link, go to the [Crash data tables](https://data.sa.gov.au/data/dataset/road-crash-data), select the dataset you want, right-click the blue **Download** button, and select 'Copy link address'.

## Extracting data from earlier years
If you need data from before 2019, the South Australian Government provides separate yearly datasets going back to 2012. Since each year comes as an individual file, we can write a simple, reusable function to download them automatically:

```python
# ---------------------------------------------------------
# Part 2: Download 2012-2018 data (each year separately)
# ---------------------------------------------------------
def download_sa_crash_data_individual_year(year_url_dict):
    """
    Download crash, vehicle, and person CSVs for each year in year_url_dict.
    The CSVs are stored as global variables named:
      raw_{year}_crash_data
      raw_{year}_vehicle_data
      raw_{year}_person_data
    """
    for year, url in year_url_dict.items():
        print(f"Downloading {year} data...")
        response = requests.get(url)
        with zipfile.ZipFile(io.BytesIO(response.content)) as z:
            # Build the expected file names inside the ZIP
            crash_file = f"{year}_DATA_SA_Crash.csv"
            vehicle_file = f"{year}_DATA_SA_Units.csv"
            person_file = f"{year}_DATA_SA_Casualty.csv"

            # Read CSVs directly from the ZIP without extracting
            globals()[f"raw_{year}_crash_data"] = pd.read_csv(z.open(crash_file), low_memory=False)
            globals()[f"raw_{year}_vehicle_data"] = pd.read_csv(z.open(vehicle_file), low_memory=False)
            globals()[f"raw_{year}_person_data"] = pd.read_csv(z.open(person_file), low_memory=False)

        print(f"Completed {year} download.\n")

# URLs for each year 2012–2018
year_urls = {
    2018: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/45ceb7e8-59bd-4492-b107-8379752ea597/download/2018_data_sa.zip",
    2017: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/8c4689ce-d3cb-413c-a467-674e00fb9115/download/2017_data_sa.zip",
    2016: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/ade3f316-3720-4c8f-b0a2-181556ec3cf6/download/2016_data_sa.zip",
    2015: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/01e6515b-5428-4850-9814-b2a11a177040/download/2015_data_sa.zip",
    2014: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/ea84f40e-8554-4f8d-bbb4-4f6b5c0ef728/download/2014_data_sa.zip", 
    2013: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/4b2b5c27-98c9-42bd-9352-3e35c4e3fc22/download/2013_data_sa.zip",
    2012: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/922cd06b-76e4-40bf-a534-ce2f2daae904/download/2012_data_sa.zip"
}

# Download all years in the 'year_urls' dictionary
download_sa_crash_data_individual_year(year_urls)
```

## Combine all crash data, vehicle data and person data (seperately) into single dataframes
From the previous steps, we now have separate DataFrames for each year from 2012 to 2018, plus a single combined DataFrame for 2019–2023. These are currently only stored in memory (they haven’t been saved to any files yet). 

| Year       | Crash data               | Vehicle data               | Person data               | 
|:-----------|------------------------: |------------------------:   |-------------------------: |
| 2012       | raw_2012_crash_data      | raw_2012_vehicle_data      | raw_2012_person_data      | 
| 2013       | raw_2013_crash_data      | raw_2013_vehicle_data      | raw_2013_person_data      | 
| 2014       | raw_2014_crash_data      | raw_2014_vehicle_data      | raw_2014_person_data      | 
| 2015       | raw_2015_crash_data      | raw_2015_vehicle_data      | raw_2015_person_data      | 
| 2016       | raw_2016_crash_data      | raw_2016_vehicle_data      | raw_2016_person_data      | 
| 2017       | raw_2017_crash_data      | raw_2017_vehicle_data      | raw_2017_person_data      | 
| 2018       | raw_2018_crash_data      | raw_2018_vehicle_data      | raw_2018_person_data      | 
| 2019-2023  | raw_2019_2023_crash_data | raw_2019_2023_vehicle_data | raw_2019_2023_person_data | 

To combine all crash data into a single DataFrame, all vehicle data into another, and all person data into a third, you can use the following code:

```python
# ---------------------------------------------------------
# Part 3: Combine all years into single DataFrames
# ---------------------------------------------------------

# Make lists of all crash, vehicle, and person DataFrames
crash_dfs = [raw_2019_2023_crash_data] + [globals()[f"raw_{year}_crash_data"] for year in year_urls]
vehicle_dfs = [raw_2019_2023_vehicle_data] + [globals()[f"raw_{year}_vehicle_data"] for year in year_urls]
person_dfs = [raw_2019_2023_person_data] + [globals()[f"raw_{year}_person_data"] for year in year_urls]

# Concatenate each list into one big DataFrame
all_crash_data = pd.concat(crash_dfs, ignore_index=True)
all_vehicle_data = pd.concat(vehicle_dfs, ignore_index=True)
all_person_data = pd.concat(person_dfs, ignore_index=True)

# Show final dataset sizes
print("\nAll data combined successfully:")
print(f"- Crash data:   {all_crash_data.shape[0]:,} rows, {all_crash_data.shape[1]} columns")
print(f"- Vehicle data: {all_vehicle_data.shape[0]:,} rows, {all_vehicle_data.shape[1]} columns")
print(f"- Person data:  {all_person_data.shape[0]:,} rows, {all_person_data.shape[1]} columns")
```

Finally, to save the raw downloaded data, we first create a folder (if it doesn’t already exist), and then save the crash, vehicle, and person datasets as separate CSV files.

```python
# ---------------------------------------------------------
# Step 4: Save combined data to CSV files
# ---------------------------------------------------------

# Folder name (will be created in the current working directory)
output_folder = "Raw SA crash data 2012 to 2023"
os.makedirs(output_folder, exist_ok=True)  # Create folder if not exists

# File paths
crash_file_path   = os.path.join(output_folder, "2012_2023_crash.csv")
vehicle_file_path = os.path.join(output_folder, "2012_2023_vehicle.csv")
person_file_path  = os.path.join(output_folder, "2012_2023_person.csv")

# Save as CSV
all_crash_data.to_csv(crash_file_path, index=False)
all_vehicle_data.to_csv(vehicle_file_path, index=False)
all_person_data.to_csv(person_file_path, index=False)

print("\nFiles saved successfully:")
print(f"- {crash_file_path}")
print(f"- {vehicle_file_path}")
print(f"- {person_file_path}\n")
```

To run this script at any time, first save your changes (Ctrl + S), then execute it using `uv` in the terminal:

```bash
uv run Download_SA_crash_data.py
```

If everything works correctly, the final output in your terminal should look something like this:

```bash
All data combined successfully:
- Crash data:   177,776 rows, 34 columns
- Vehicle data: 379,600 rows, 18 columns
- Person data:  72,409 rows, 12 columns

Files saved successfully:
- Raw SA crash data 2012 to 2023\2012_2023_crash.csv
- Raw SA crash data 2012 to 2023\2012_2023_vehicle.csv
- Raw SA crash data 2012 to 2023\2012_2023_person.csv
```
