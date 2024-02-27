# SQLProject
SQL Data cleaning project

Nashville Housing Data Cleaning Project

**Overview**

This project focuses on cleaning a large dataset containing Nashville housing data. The dataset comprises 19 columns and 56,477 rows and requires various cleaning tasks to ensure data quality and usability.

**Purpose**

The primary objective of this project is to prepare the Nashville housing data for analysis by addressing issues such as missing values, inconsistent formats, and redundant information. By cleaning the dataset, we aim to facilitate accurate analysis and decision-making based on the data.

**Steps Taken**

Date Format Conversion: The date format was converted from Date/Time to Date to remove unnecessary time information.
Property Address Population: Blank values in the PropertyAddress column were populated using existing ParcelID matches.
Address Splitting: The PropertyAddress and OwnerAddress columns were split into separate columns for better organization and analysis.
SoldAsVacant Field Editing: Values in the SoldAsVacant field were edited to standardize 'Y' and 'N' values to 'Yes' and 'No' respectively.
Duplicate Removal: Duplicate rows were removed based on specific columns to ensure data integrity.
Unused Column Deletion: Columns that were no longer needed after cleaning, such as SaleDate, PropertyAddress, OwnerAddress, and TaxDistrict, were dropped from the dataset.

**Files**

Data Cleaning Code.sql: SQL script containing the code used for cleaning the Nashville housing dataset.
Nashville Housing Data for Data Cleaning.xlsx: Data used prior to cleaning.


**Usage**

Open the data_cleaning_script.sql file in your preferred SQL environment.
Execute the script to clean the Nashville housing dataset.
Refer to the comments within the script for explanations of each cleaning step.
After cleaning, the dataset is ready for further analysis or integration into other projects.

**Dependencies**

SQL environment capable of executing SQL scripts.
Access to the Nashville housing dataset (provided in PortfolioProjects.dbo.NashvilleHousing table).

**Contributors**

Craig Wilson

**License**

This project is licensed under the MIT License.
