# Public crash data analysis: Yearly trends

> This tutorial will walk you through analysing the yearly trends of crashes in South Australia.

Using [downloaded](https://centre-for-automotive-safety-research.github.io/2025/06/11/public-crash-data-2-extracting-crash-data-sa.html) and [merged](https://centre-for-automotive-safety-research.github.io/2025/06/12/public-crash-data-3-merging-crash-data.html) public South Australian crash data, we will create graphs to display annual crash trends from 2012 to 2023.

## Import the libraries: Matplotlib and Seaborn
These libraries will let us create professional-looking charts and customise their style.

In the terminal, run:
```bash
uv add matplotlib seaborn
```

## Create a new Python script
Inside your project folder, create a new file called `3.Yearly_stats_SA_crashes.py`. 

## Create an output folder and load previously downloaded crash data
Since we’ll be generating several graphs, it’s handy to save them automatically. Part 1 of the following code creates an output folder called `Output_graphs`. Part 2 loads the previously downloaded crash data from CSV into a Pandas DataFrame.

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import os
from matplotlib.ticker import StrMethodFormatter

# ---------------------------
# Part 1: Create output folder to save graphs
# ---------------------------
# Folder name (will be created in the current working directory)
output_folder = "Output_graphs"
os.makedirs(output_folder, exist_ok=True)  # Create folder if not exists

# ---------------------------
# Part 2: Load crash data
# ---------------------------
input_folder = "Raw_SA_crash_data_2012_2023"
crash_file = os.path.join(input_folder, "2012_2023_crash.csv")

crash_df = pd.read_csv(crash_file, low_memory=False)
```

## Plot yearly totals of crashes
Here we calculate the total number of crashes per year and plot them as a simple line graph.
```python
# ---------------------------
# Part 3: Plot yearly totals of crashes (line graph)
# ---------------------------
yearly_totals = crash_df.groupby('Year').size().reset_index(name='Total_Crashes')

plt.figure(figsize=(10, 6))
sns.lineplot(data=yearly_totals, x='Year', y='Total_Crashes', marker='o', color='blue', 
             markersize=5)
plt.title("Total crashes in South Australia per year (2012-2023)")
plt.xlabel("Year")
plt.ylabel("Number of crashes")
plt.yticks(np.arange(0, yearly_totals['Total_Crashes'].max() + 2000, 2000))
plt.xticks(ticks=yearly_totals['Year'], rotation=45)
plt.ylim(bottom=0)
plt.gca().yaxis.set_major_formatter(StrMethodFormatter('{x:,.0f}')) # Format y-axis with commas
plt.grid(axis='y', color='grey', linestyle='--', linewidth=0.5) # Horizontal grey lines
plt.tight_layout()

# Save the figure
output_file = os.path.join(output_folder, "2012_2023_yearly_crash_totals.png")
plt.savefig(os.path.join(output_folder, "2012_2023_yearly_crash_totals.png"), dpi=300)
print(f"Yearly crash totals graph saved to: {output_file}")

plt.show()
```
The generated graph will look like this:
![Alt text](https://raw.githubusercontent.com/Centre-for-Automotive-Safety-Research/centre-for-automotive-safety-research.github.io/main/_posts_images/2012_2023_yearly_crash_totals.png)

## Plot crashes by severity (all categories)
The data contains a crash severity code `CSEF Severity`.
Here we:
1. Replace the numeric severity codes with descriptive text.
2. Assign a colour to each severity type.
3. Plot separate lines for each severity category on the same chart.

This lets us see how different types of crashes have changed over the years.

```python
# ---------------------------
# Part 4a: Plot distribution of injury severity across years (all severities)
# ---------------------------

# Change severity codes to more descriptive labels
severity_mapping = {
    "1: PDO": "Property damage only",
    "2: MI": "Minor injury",
    "3: SI": "Serious injury",
    "4: Fatal": "Fatal"
}

crash_df['CSEF Severity'] = crash_df['CSEF Severity'].map(severity_mapping)

# Custom colors for each severity
severity_colors = {
    "Fatal": "#000000",       # black
    "Serious injury": "#FF0000",     # red
    "Minor injury": "#ff8800",       # orange
    "Property damage only": "#2ca02c"   # green
}

# Aggregate counts per year and severity
severity_yearly_counts = crash_df.groupby(['Year', 'CSEF Severity']).size().reset_index(name='Count')

plt.figure(figsize=(12, 6))

severity_order = ["Property damage only", "Minor injury", "Serious injury", "Fatal"]

# Plot each severity as a separate line
for severity in severity_order:
    color = severity_colors[severity]
    subset = severity_yearly_counts[severity_yearly_counts['CSEF Severity'] == severity]
    plt.plot(subset['Year'], subset['Count'], label=severity, color=color, marker='o', markersize=3)


plt.title("Total crashes in South Australia per year (2012-2023), by overall crash severity")
plt.xlabel("Year")
plt.ylabel("Number of crashes")
plt.ylim(bottom=0)
plt.xticks(subset['Year'].unique(), rotation=45)  # show every year
plt.grid(axis='y', color='grey', linestyle='--', linewidth=0.5)
plt.gca().yaxis.set_major_formatter(StrMethodFormatter('{x:,.0f}')) # Format y-axis with commas
plt.legend(title="Overall crash severity")
plt.tight_layout()

# Save figure
output_file = os.path.join(output_folder, "2012_2023_injury_severity_counts_per_year.png")
plt.savefig(output_file, dpi=300)
print(f"Yearly crash totals graph saved to: {output_file}")

plt.show()
```

The generated graph will look like this:
![Alt text](https://raw.githubusercontent.com/Centre-for-Automotive-Safety-Research/centre-for-automotive-safety-research.github.io/main/_posts_images/2012_2023_injury_severity_counts_per_year.png)

## Plot only serious injury and fatal crashes
Sometimes we’re only interested in the most severe crash outcomes.

This plot filters the data to only “Serious injury” and “Fatal” crashes so we can focus on their trends without the other lines.

```python
# ---------------------------
# Part 4b: Plot only Serious injury and Fatal counts
# ---------------------------

plt.figure(figsize=(12, 6))
severity_subset_order = ["Serious injury", "Fatal"]

for severity in severity_subset_order:
    color = severity_colors[severity]
    subset = severity_yearly_counts[severity_yearly_counts['CSEF Severity'] == severity]
    plt.plot(subset['Year'], subset['Count'], label=severity, color=color, marker='o', markersize=3)

plt.title("Serious injury and fatal crashes in South Australia per year (2012-2023)")
plt.xlabel("Year")
plt.ylabel("Number of crashes")
plt.ylim(bottom=0)
plt.xticks(subset['Year'].unique(), rotation=45)
plt.grid(axis='y', color='grey', linestyle='--', linewidth=0.5)
plt.gca().yaxis.set_major_formatter(StrMethodFormatter('{x:,.0f}'))
plt.legend(title="Overall crash severity")
plt.tight_layout()

output_file_subset = os.path.join(output_folder, "2012_2023_serious_and_fatal_counts_per_year.png")
plt.savefig(output_file_subset, dpi=300)
print(f"Serious injury + fatal crash graph saved to: {output_file_subset}")

plt.show()
```
The generated graph will look like this:
![Alt text](https://raw.githubusercontent.com/Centre-for-Automotive-Safety-Research/centre-for-automotive-safety-research.github.io/main/_posts_images/2012_2023_serious_and_fatal_counts_per_year.png)

## Run the script
As always, remember to save the script (Ctrl + S), then run it in your terminal using:
```bash
uv run 3.Yearly_stats_SA_crashes.py
```

*Note: when Python reaches `plt.show()`, it will pause and display the plot window. Close the window to continue to the next plot.*
