# Blog #003: Downloading SA's public crash data

Now that we’ve covered the basics of crash data structure (see [Blog 002](https://centre-for-automotive-safety-research.github.io/2025/06/10/blog2-understanding-carsh-data.html) if you missed it), let’s explore where and how to access South Australia’s crash data.

### Exploring Data.SA crash data

The South Australian Government’s Department for Infrastructure and Transport (DIT) provides crash data in two main formats:

- **Data tables:** Detailed datasets for crashes, vehicles, and people involved.  
  Available here: [Crash data tables](https://data.sa.gov.au/data/dataset/road-crash-data)  
- **Crash locations:** Geospatial data showing crash locations on maps.  
  Available here: [Crash location maps](https://data.sa.gov.au/data/dataset/road-crashes-in-sa)  

Note that the geospatial data includes only the crash-level information - vehicle and person details are *not* included.

---

## Extracting most recent (2019-2023) crash data from Data.SA

Let’s walk through how to download and load the most recent (2019-2023) crash, vehicle, and person data from the Data.SA's data tables using Python.

Start by opening your Python editor. If you need help setting up your environment, see [Blog 001](https://centre-for-automotive-safety-research.github.io/2025/06/09/blog1-setting-up-python.html).

You’ll need a few libraries:

- `pandas`: a powerful data manipulation and analysis toolkit  
- `zipfile`: for handling ZIP files  
- `requests`: to download files from the web  
- `io`: for handling in-memory file operations  

If you do not have these libraries installed, you will need to install them. Use pip in your Python environment:

```bash
pip install pandas
pip install requests
```
(Note: `zipfile` and `io` are part of the Python standard library and do not require installation.)

Now, here’s an example of how to download the crash data for 2019 to 2023:

```python
import pandas as pd
import zipfile
import requests
import io

#%% Download 2019 to 2023 crash, vehicle, person data from DataSA

# 1: Download 2019-2023 ZIP file
zip_url_2019_2023 = "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/78d24425-6c14-426e-8895-d414c2a12521/download/2019-2023_data_sa_as_at_20240913.zip"
response = requests.get(zip_url_2019_2023)

# 2: Open ZIP in memory
with zipfile.ZipFile(io.BytesIO(response.content)) as z:
    # List all files inside the 2019-2023 ZIP - optional (uncomment/remove '#' at start of line if you want to print the following information)
    # print("Files in 2019-2023 ZIP:", z.namelist())
    
    # 3: Read each CSV into a DataFrame directly from the ZIP
    print("Downloading data for 2019-2023")
    raw_2019_2023_crash_data = pd.read_csv(z.open("2019-2023_DATA_SA_Crash.csv"), low_memory=False)
    raw_2019_2023_vehicle_data = pd.read_csv(z.open("2019-2023_DATA_SA_Units.csv"), low_memory=False)
    raw_2019_2023_person_data = pd.read_csv(z.open("2019-2023_DATA_SA_Casualty.csv"), low_memory=False)
    print("Completed download for 2019-2023\n")
```
## Accessing earlier years
If you want data from before 2019, the South Australian Government provides yearly datasets dating back to 2012. Because these are individual files per year, we can create a simple function to download each one automatically:

```python
#%% Download 2012 to 2018 (individually) crash, vehicle, person data from DataSA

def download_sa_crash_data_individual_year(year_url_dict):
    """
    Downloads and extracts crash, vehicle, and person data CSVs
    from Data.SA ZIP files for multiple years, assigning them to global variables.

    Variables created for each year:
        raw_{year}_crash_data
        raw_{year}_vehicle_data
        raw_{year}_person_data

    Parameters:
        year_url_dict (dict): keys = year (int or str),
                              values = ZIP download URL (str)
    """
    for year, url in year_url_dict.items():
        print(f"Downloading data for {year}...")
        response = requests.get(url)
        with zipfile.ZipFile(io.BytesIO(response.content)) as z:
            # List all files inside the {year} ZIP - optional (uncomment/remove '#' at start of line if you want to print the following information)
            # print(f"Files in {year} ZIP:", z.namelist())

            crash_file = f"{year}_DATA_SA_Crash.csv"
            vehicle_file = f"{year}_DATA_SA_Units.csv"
            person_file = f"{year}_DATA_SA_Casualty.csv"

            globals()[f"raw_{year}_crash_data"] = pd.read_csv(z.open(crash_file), low_memory=False)
            globals()[f"raw_{year}_vehicle_data"] = pd.read_csv(z.open(vehicle_file), low_memory=False)
            globals()[f"raw_{year}_person_data"] = pd.read_csv(z.open(person_file), low_memory=False)

        print(f"Completed download for {year}\n")

# Creation of year_urls with key=year, value=zip download link
year_urls = {
    2018: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/45ceb7e8-59bd-4492-b107-8379752ea597/download/2018_data_sa.zip",
    2017: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/8c4689ce-d3cb-413c-a467-674e00fb9115/download/2017_data_sa.zip",
    2016: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/ade3f316-3720-4c8f-b0a2-181556ec3cf6/download/2016_data_sa.zip",
    2015: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/01e6515b-5428-4850-9814-b2a11a177040/download/2015_data_sa.zip",
    2014: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/ea84f40e-8554-4f8d-bbb4-4f6b5c0ef728/download/2014_data_sa.zip", 
    2013: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/4b2b5c27-98c9-42bd-9352-3e35c4e3fc22/download/2013_data_sa.zip",
    2012: "https://data.sa.gov.au/data/dataset/21386a53-56a1-4edf-bd0b-61ed15f10acf/resource/922cd06b-76e4-40bf-a534-ce2f2daae904/download/2012_data_sa.zip"
}

download_sa_crash_data_individual_year(year_urls)
```
Now, you will have the following dataframes of crash, vehicle and person data:
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
