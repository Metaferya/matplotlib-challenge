# Project purpose & Overview

The purpose of this study was to compare the performance of Pymaceuticals’ drug of interest, Capomulin, against the other treatment regimens. 

The analysis uses data from a study conducted by Pymaceuticals Inc. to compare the impact of different treatments on tumor growth. The study 

focuses on metrics such as tumor volume,survival rates, and correlations between different variables.

# Table of Contents

## Analysis

## Dependencies and Setup

## Data Collection and Cleaning

## Summary Statistics

## Bar and Pie Charts

## Quartiles, Outliers, and Boxplots

## Line and Scatter Plots

## Correlation and Regression

## Credits


# Analysis

***1-  Among the various drug regimens studied, Capomulin had the highest number of mice that completed the treatment. Except for Remicane,

all other regimens experienced a noticeable number of mice deaths over the course of the study. This suggests that Capomulin not only reduces

tumor growth but also supports higher survival rates.***

***2-	Among the various drug regimens studied, Capomulin had the highest number of mice that completed the treatment. Except for Remicane,

all other regimens experienced a noticeable number of mice deaths over the course of the study. This suggests that Capomulin not only reduces 

tumor growth but also supports higher survival rates.***

***3-  There is a strong positive correlation between mouse weight and tumor volume. This indicates that heavier mice tend to have larger tumors.

 This relationship suggests that mouse weight could be a contributing factor to the efficacy of the drug regimens, potentially affecting the absorption

 or distribution of the drugs within the body.***

 ***4- Within the Infobinol treatment group, a potential outlier was observed. While the majority of mice in this regimen exhibited an increase in tumor
 
  volume, one mouse showed a notable reduction in tumor growth.*** 

# Dependencies and Setup

Ensure you have the following dependencies installed:

import matplotlib.pyplot as plt

import pandas as pd

import scipy.stats as st # type: ignore

# Data Collection and Cleaning

**Load the data**

mouse_metadata_path = "data/Mouse_metadata.csv"

study_results_path = "data/Study_results.csv"

mouse_metadata = pd.read_csv(mouse_metadata_path)

study_results = pd.read_csv(study_results_path)

**Combine the data into a single DataFrame**

mouse_study_complete = pd.merge(study_results, mouse_metadata, how="left", on="Mouse ID")

mouse_study_complete.head()

**Check for duplicate mice**

duplicates = mouse_study_complete.loc[mouse_study_complete.duplicated(subset=['Mouse ID', 'Timepoint']), "Mouse ID"].unique()

duplicates_data = mouse_study_complete.loc[mouse_study_complete["Mouse ID"] == 'g989']

print(duplicates_data)

**Clean the Data**

***Remove duplicates***
mouse_study_complete_cleaned = mouse_study_complete[mouse_study_complete['Mouse ID'].isin(duplicates)==False]

cleaned_mouse_count = mouse_study_complete_cleaned["Mouse ID"].nunique()

print(f"Number of mice after cleaning: {cleaned_mouse_count}")

# Summary Statistics

Generate summary statistics for each drug regimen:

**Calculate summary statistics**

means = mouse_study_complete_cleaned.groupby('Drug Regimen').mean(numeric_only=True)['Tumor Volume (mm3)']

medians = mouse_study_complete_cleaned.groupby('Drug Regimen').median(numeric_only=True)['Tumor Volume (mm3)']

variances = mouse_study_complete_cleaned.groupby('Drug Regimen').var(numeric_only=True)['Tumor Volume (mm3)']

std_devs = mouse_study_complete_cleaned.groupby('Drug Regimen').std(numeric_only=True)['Tumor Volume (mm3)']

sems = mouse_study_complete_cleaned.groupby('Drug Regimen').sem(numeric_only=True)['Tumor Volume (mm3)']

**Assemble into a summary DataFrame**

summary_table = pd.DataFrame({

    "Mean Tumor Volume": means,

    "Median Tumor Volume": medians,

    "Tumor Volume Variance": variances,

    "Tumor Volume Std. Dev.": std_devs,

    "Tumor Volume Std. Err.": sems
})

print(summary_table)

# Bar and Pie Charts


**Generate bar plots**

counts = mouse_study_complete_cleaned['Drug Regimen'].value_counts()

counts.plot(kind='bar', color="orange", alpha=0.90)

plt.xlabel('Drug Regimen')

plt.ylabel("Number of Observed Mouse Timepoints")

plt.xticks(rotation=45)

plt.show()

**Using pyplot**

plt.bar(counts.index.values, counts.values, color="red", alpha=0.90)

plt.xlabel('Drug Regimen')

plt.ylabel("Number of Observed Mouse Timepoints")

plt.xticks(rotation=45)

plt.show()

**Generate pie charts**

sexs = mouse_study_complete_cleaned.Sex.value_counts()

sexs.plot(kind='pie', autopct='%1.1f%%')

plt.show()

**Using pyplot**

plt.pie(sexs.values, labels=sexs.index.values, autopct='%3.1f%%')

plt.ylabel("Sex")

plt.show()

# Quartiles, Outliers, and Boxplots

**Determine IQR and identify potential outliers**
highest_tumor = mouse_study_complete_cleaned.groupby(['Mouse ID'])['Timepoint'].max().reset_index()

new_data = highest_tumor.merge(mouse_study_complete_cleaned, on=['Mouse ID', 'Timepoint'], how='left')

treatment_list = ['Capomulin', 'Ramicane', 'Infubinol', 'Ceftamin']

tumor_volume_list = []

for drug in treatment_list:

    final_tumor_volume = new_data.loc[new_data["Drug Regimen"] == drug, "Tumor Volume (mm3)"]

    tumor_volume_list.append(final_tumor_volume)

    quartiles = final_tumor_volume.quantile([0.25, 0.5, 0.75])

    lower_quartiles = quartiles[0.25]

    upper_quartiles = quartiles[0.75]

    iqr = upper_quartiles - lower_quartiles

    lower_bound = lower_quartiles - (1.5 * iqr)

    upper_bound = upper_quartiles + (1.5 * iqr)

    outliers = final_tumor_volume.loc[(final_tumor_volume < lower_bound) | (final_tumor_volume > upper_bound)]

    print(f"{drug}'s potential outliers: {outliers}")

**Generate a box plot**
marker = dict(markerfacecolor='yellow', alpha=1, markersize=20)

plt.boxplot(tumor_volume_list, labels=treatment_list, flierprops=marker)

plt.ylabel('Final Tumor Volume (mm3)')

plt.show()

# Line and Scatter Plots

**Line plot of tumor volume vs. time point for a single mouse treated with Capomulin**

capomulin_tabel = mouse_study_complete_cleaned.loc[mouse_study_complete_cleaned["Drug Regimen"] == "Capomulin"]

mouse_data = capomulin_tabel.loc[capomulin_tabel["Mouse ID"] == "r944"]

plt.plot(mouse_data['Timepoint'], mouse_data['Tumor Volume (mm3)'])

plt.xlabel("Timepoints in Days")

plt.ylabel("Tumor Volume (mm3)")

plt.title("Capomulin treatment of Mouse r944")

# Correlation and Regression

**Get the correlation and regression**

correlation=round(st.pearsonr(capomulin_avg['Weight (g)'], capomulin_avg['Tumor Volume (mm3)'])[0],2)

print(f"The Correlation between mouse weight and average mouse tumor is {correlation}")

model = st.linregress(capomulin_avg['Weight (g)'],capomulin_avg['Tumor Volume (mm3)'])

y_values = capomulin_avg['Weight (g)']*model[0]+model[1]

plt.scatter(capomulin_avg['Weight (g)'],capomulin_avg['Tumor Volume (mm3)'])

plt.plot(capomulin_avg['Weight (g)'], y_values, color='red')

plt.xlabel("Weight (g)")

plt.ylabel("Average Tumor Volume (mm3)")

plt.show()         

# Credits

I would like to extend my acknoledgment to my instructor and xpert for the help and support.






