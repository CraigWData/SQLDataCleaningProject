-- SQL Cleaning Project - Nashville Housing Data
-- Imported large data set for cleaning 19 columns and 56,477 rows

SELECT *
FROM PortfolioProjects.dbo.NashvilleHousing

-- Convert the date format from Date/Time to Date as the time is not necessary in this dataset

SELECT SaleDate, CONVERT(Date,SaleDate)
FROM PortfolioProjects.dbo.NashvilleHousing


UPDATE NashvilleHousing
SET SaleDate = CONVERT(Date,SaleDate)

-- UPDATE statement didn't work. Attempting alternative method.

-- Adding a new column to hold the converted SaleDate values
ALTER TABLE NashvilleHousing
ADD SaleDateConverted Date; 

-- Converting and populating the new SaleDateConverted column
UPDATE NashvilleHousing
SET SaleDateConverted = CONVERT(Date,SaleDate)

-- Confirming the updated SaleDateConverted column
SELECT SaleDateConverted
FROM PortfolioProjects.dbo.NashvilleHousing


-----------------------------------------------------------------------------------------------

-- Populate PropertyAddress data as it contains blank values

-- Identify rows with missing PropertyAddress

SELECT *
FROM PortfolioProjects.dbo.NashvilleHousing
WHERE PropertyAddress IS NULL

-- We observe that each property address has a unique ParcelID. Total ParcelID count is 48,559, while total row count/UniqueID count is 56,477.

Select COUNT(Distinct ParcelID) AS ParcelIDCount, Count(Distinct UniqueID) AS UniqueIDCount
FROM PortfolioProjects.dbo.NashvilleHousing

-- Ordering the dataset by ParcelID for further analysis


SELECT *
FROM PortfolioProjects.dbo.NashvilleHousing
ORDER BY ParcelID

-- Populate null PropertyAddress values using existing ParcelID matches

SELECT L.ParcelID, L.PropertyAddress, R.ParcelID, R.PropertyAddress, ISNULL(L.PropertyAddress, R.PropertyAddress)
FROM PortfolioProjects.dbo.NashvilleHousing AS L -- LEFT
JOIN PortfolioProjects.dbo.NashvilleHousing AS R --RIGHT
	ON L.ParcelID = R.ParcelID
	AND L.[UniqueID ] <> R.[UniqueID ]
WHERE L.PropertyAddress IS NULL

-- Update the null PropertyAddress values with accurate ones

UPDATE L
SET PropertyAddress = ISNULL(L.PropertyAddress, R.PropertyAddress)
FROM PortfolioProjects.dbo.NashvilleHousing AS L -- LEFT
JOIN PortfolioProjects.dbo.NashvilleHousing AS R --RIGHT
	ON L.ParcelID = R.ParcelID
	AND L.[UniqueID ] <> R.[UniqueID ]
	WHERE L.PropertyAddress IS NULL

-- There are no longer NULL values in the PropertyAddress Column

SELECT *
FROM PortfolioProjects.dbo.NashvilleHousing
WHERE PropertyAddress IS NULL
ORDER BY ParcelID

-----------------------------------------------------------------------------------------------

-- Splitting Addresses into multiple columns (PropertyAddress and OwnerAddress)
-- Fortunately, all cells contain the delimiter ","

SELECT PropertyAddress, OwnerAddress
FROM PortfolioProjects.Dbo.NashvilleHousing

-- Split PropertyAddress into two columns (Address, City)

SELECT PropertyAddress
FROM PortfolioProjects.Dbo.NashvilleHousing

-- Adding new columns to hold split PropertyAddress values

SELECT 
SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress)-1) AS Address
, 
FROM PortfolioProjects.Dbo.NashvilleHousing

-- Updating PropertySplitAddress column with split Address values

ALTER TABLE NashvilleHousing
ADD PropertySplitAddress nvarchar(255); 

-- Adding a new column to hold the split city values

UPDATE NashvilleHousing
SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress)-1)

ALTER TABLE NashvilleHousing
ADD PropertySplitCity nvarchar(255);; 

-- Updating PropertySplitCity column with split City values

UPDATE NashvilleHousing
SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress)+1, LEN(PropertyAddress))

-- PropertyAddress is now split and much more usable

-- Split OwnerAddress into multiple columns (Address, City, State)

SELECT OwnerAddress
FROM PortfolioProjects.Dbo.NashvilleHousing

-- Splitting OwnerAddress into separate columns (Address, City, State)

SELECT
PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3)
, PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2)
, PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1)
FROM PortfolioProjects.Dbo.NashvilleHousing

-- Adding new columns to hold split OwnerAddress values

ALTER TABLE NashvilleHousing
	ADD OwnerSplitAddress nvarchar(255);
	ADD OwnerSplitCity nvarchar(255);
	ADD OwnerSplitState nvarchar(255);

-- Updating new columns with split OwnerAddress values

UPDATE NashvilleHousing
	SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3)
	SET OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2)
	SET OwnerSplitState = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1)


-- OwnerAddress is now split and much more usable

-----------------------------------------------------------------------------------------------

-- Edit "SoldAsVacant" field to change "N" and "Y" values to "No" and "Yes"

SELECT Distinct(SoldAsVacant), COUNT(SoldAsVacant)
FROM PortfolioProjects.dbo.NashvilleHousing
GROUP BY SoldAsVacant
ORDER BY 2


-- Updating SoldAsVacant values to "Yes" and "No"

UPDATE NashvilleHousing
SET SoldAsVacant = CASE WHEN SoldAsVacant = 'Y' THEN 'Yes'
	   WHEN SoldAsVacant = 'N' THEN 'No'
	   ELSE SoldAsVacant
		END


-----------------------------------------------------------------------------------------------

-- Remove Duplicates
-- Ignoring the UniqueID and focusing on duplicates where cells in a row should be unique (ParcelID, SaleDate, LegalReference)

WITH RowNumCTE AS(
	SELECT *, 
		ROW_NUMBER() OVER(
			PARTITION BY ParcelID, 
						 PropertyAddress, 
						 SalePrice, 
						 SaleDate, 
						LegalReference
						ORDER BY 
							UniqueID
							) row_num
	FROM PortfolioProjects.dbo.NashvilleHousing
)
-- Removing duplicate rows
DELETE
FROM RowNumCTE
WHERE row_num>1

-----------------------------------------------------------------------------------------------

-- Delete Unused Columns. Given that SaleDate, OwnerAddress, and Property Address have been updated or split, they are no longer needed. TaxDistrict is not needed either.

Select *
FROM PortfolioProjects.dbo.NashvilleHousing

Absolutely, here are the improved comments integrated into your script:

sql
Copy code
-- SQL Cleaning Project - Nashville Housing Data
-- Imported large data set for cleaning 19 columns and 56,477 rows

-- Convert the date format from Date/Time to Date as the time is not necessary in this dataset
SELECT SaleDate, CONVERT(Date, SaleDate)
FROM PortfolioProjects.dbo.NashvilleHousing

-- UPDATE statement didn't work. Attempting alternative method.
-- ^^ This did not work

-- Adding a new column to hold the converted SaleDate values
ALTER TABLE PortfolioProjects.dbo.NashvilleHousing
ADD SaleDateConverted Date; 

-- Converting and populating the new SaleDateConverted column
UPDATE PortfolioProjects.dbo.NashvilleHousing
SET SaleDateConverted = CONVERT(Date, SaleDate)

-- Check the SaleDateConverted column
SELECT SaleDateConverted
FROM PortfolioProjects.dbo.NashvilleHousing


-----------------------------------------------------------------------------------------------

-- Populate PropertyAddress data as it contains blank values

-- Identify rows with missing PropertyAddress
SELECT *
FROM PortfolioProjects.dbo.NashvilleHousing
WHERE PropertyAddress IS NULL

-- We observe that each property address has a unique ParcelID. Total ParcelID count is 48,559, while total row count/UniqueID count is 56,477.
Select COUNT(Distinct ParcelID) AS ParcelIDCount, Count(Distinct UniqueID) AS UniqueIDCount
FROM PortfolioProjects.dbo.NashvilleHousing

-- Ordering the dataset by ParcelID for further analysis
SELECT *
FROM PortfolioProjects.dbo.NashvilleHousing
ORDER BY ParcelID

-- Populate null PropertyAddress values using existing ParcelID matches
SELECT L.ParcelID, L.PropertyAddress, R.ParcelID, R.PropertyAddress, ISNULL(L.PropertyAddress, R.PropertyAddress)
FROM PortfolioProjects.dbo.NashvilleHousing AS L -- LEFT
JOIN PortfolioProjects.dbo.NashvilleHousing AS R -- RIGHT
	ON L.ParcelID = R.ParcelID
	AND L.[UniqueID ] <> R.[UniqueID ]
WHERE L.PropertyAddress IS NULL

-- Update the null PropertyAddress values with accurate ones
UPDATE L
SET PropertyAddress = ISNULL(L.PropertyAddress, R.PropertyAddress)
FROM PortfolioProjects.dbo.NashvilleHousing AS L -- LEFT
JOIN PortfolioProjects.dbo.NashvilleHousing AS R -- RIGHT
	ON L.ParcelID = R.ParcelID
	AND L.[UniqueID ] <> R.[UniqueID ]
WHERE L.PropertyAddress IS NULL

-- There are no longer NULL values in the PropertyAddress Column

-----------------------------------------------------------------------------------------------

-- Splitting Addresses into multiple columns (PropertyAddress and OwnerAddress)
-- Fortunately, all cells contain the delimiter ","
SELECT PropertyAddress, OwnerAddress
FROM PortfolioProjects.dbo.NashvilleHousing

-- Split PropertyAddress into two columns (Address, City)
SELECT 
    SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress)-1) AS Address
FROM PortfolioProjects.dbo.NashvilleHousing

-- Adding new columns to hold split PropertyAddress values
ALTER TABLE PortfolioProjects.dbo.NashvilleHousing
ADD PropertySplitAddress nvarchar(255); 

-- Updating PropertySplitAddress column with split Address values
UPDATE PortfolioProjects.dbo.NashvilleHousing
SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress)-1)

-- Adding a new column to hold the split city values
ALTER TABLE PortfolioProjects.dbo.NashvilleHousing
ADD PropertySplitCity nvarchar(255); 

-- Updating PropertySplitCity column with split City values
UPDATE PortfolioProjects.dbo.NashvilleHousing
SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress)+1, LEN(PropertyAddress))

-- PropertyAddress is now split and much more usable

-- Split OwnerAddress into multiple columns (Address, City, State)
SELECT OwnerAddress
FROM PortfolioProjects.dbo.NashvilleHousing

-- Splitting OwnerAddress into separate columns (Address, City, State)
SELECT
    PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3) AS Address,
    PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2) AS City,
    PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1) AS State
FROM PortfolioProjects.dbo.NashvilleHousing

-- Adding new columns to hold split OwnerAddress values
ALTER TABLE PortfolioProjects.dbo.NashvilleHousing
ADD OwnerSplitAddress nvarchar(255),
    OwnerSplitCity nvarchar(255),
    OwnerSplitState nvarchar(255);

-- Updating new columns with split OwnerAddress values
UPDATE PortfolioProjects.dbo.NashvilleHousing
SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3),
    OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2),
    OwnerSplitState = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1)

-- OwnerAddress is now split and much more usable

-----------------------------------------------------------------------------------------------

-- Edit "SoldAsVacant" field to change "N" and "Y" values to "No" and "Yes"
SELECT DISTINCT SoldAsVacant, COUNT(SoldAsVacant)
FROM PortfolioProjects.dbo.NashvilleHousing
GROUP BY SoldAsVacant
ORDER BY 2

-- Updating SoldAsVacant values to "Yes" and "No"
UPDATE PortfolioProjects.dbo.NashvilleHousing
SET SoldAsVacant = CASE 
                        WHEN SoldAsVacant = 'Y' THEN 'Yes'
                        WHEN SoldAsVacant = 'N' THEN 'No'
                        ELSE SoldAsVacant
                   END

-----------------------------------------------------------------------------------------------

-- Remove Duplicates
-- Ignoring the UniqueID and focusing on duplicates where cells in a row should be unique (ParcelID, SaleDate, LegalReference)
WITH RowNumCTE AS (
    SELECT *, 
        ROW_NUMBER() OVER (
            PARTITION BY ParcelID, PropertyAddress, SalePrice, SaleDate, LegalReference
            ORDER BY UniqueID
        ) row_num
    FROM PortfolioProjects.dbo.NashvilleHousing
)

-- Removing duplicate rows
DELETE FROM RowNumCTE
WHERE row_num > 1

-----------------------------------------------------------------------------------------------

-- Delete Unused Columns. Given that SaleDate, OwnerAddress, and Property Address have been updated or split, they are no longer needed. TaxDistrict is not needed either.
SELECT *
FROM PortfolioProjects.dbo.NashvilleHousing

-- Dropping unnecessary columns

ALTER TABLE NashvilleHousing
DROP COLUMN SaleDate, PropertyAddress, OwnerAddress, TaxDistrict