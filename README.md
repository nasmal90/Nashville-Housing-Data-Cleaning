# 🏘️ Nashville Housing Data Cleaning

## 📋 Project Overview
This project focuses on **cleaning** and **standardizing** a dataset of over 56,000 records from Nashville's housing market. It applies various data cleaning techniques to improve the dataset's **quality**, **usability**, and **accuracy**. The techniques include handling missing values, standardizing formats, and removing duplicates.

## 🎯 Objectives
The primary goals of this project are:
- 🧹 **Standardize** and **clean** the dataset to enhance usability in analysis.
- 🛠️ Apply common **SQL data cleaning** techniques such as handling null values, standardizing date formats, and removing duplicates.
- 🏡 **Populate missing property addresses** using matching parcel IDs.

## 💻 Tools Used
- **SQL**: Used for executing various data cleaning and transformation queries.

## 📊 Dataset Description
Key fields in the dataset include:
- **Parcel ID**: A unique identifier for each property.
- **Address**: The property’s physical address.
- **Sale Date**: The date the property was sold.
- **Price**: The sale price of the property.

## 🔑 Key Steps in Data Cleaning

### 1. 🚮 Removing Duplicates
To remove duplicate rows, we kept only the most recent sale record based on the `ParcelID`. The following SQL query achieves this:

```sql
WITH CTE AS (
    SELECT *,
        ROW_NUMBER() OVER (PARTITION BY ParcelID ORDER BY SaleDate DESC) AS RowNum
    FROM NashvilleHousing
)
DELETE FROM CTE
WHERE RowNum > 1;
```
This query partitions the dataset by ParcelID and identifies duplicate records using ROW_NUMBER(). Only the most recent record is kept based on the SaleDate.

### 2. 📅 Standardizing Date Format
To ensure consistency, the SaleDate was standardized by removing the time elements. The following SQL query was used:

```
UPDATE NashvilleHousing
SET SaleDate = CONVERT(DATE, SaleDate);
```
This query converts the SaleDate to the DATE format, ensuring uniformity across the dataset.

### 3. 🏠 Filling Missing Addresses
Missing addresses were filled using a self-join technique based on ParcelID. The following SQL query accomplishes this:
```
UPDATE nh
SET nh.Address = nh2.Address
FROM NashvilleHousing nh
INNER JOIN NashvilleHousing nh2
ON nh.ParcelID = nh2.ParcelID
WHERE nh.Address IS NULL AND nh2.Address IS NOT NULL;
```
This query populates missing Address values by matching records with the same ParcelID but a non-null address.

### 4. 🔗 Address Splitting
To separate concatenated address fields into distinct columns, we used SQL functions like SUBSTRING and CHARINDEX. The following query extracts the street address and city:

```
SELECT
    SUBSTRING(Address, 1, CHARINDEX(',', Address) - 1) AS StreetAddress,
    SUBSTRING(Address, CHARINDEX(',', Address) + 1, LEN(Address)) AS City
FROM NashvilleHousing;
```
This query splits the address field into StreetAddress and City for better usability in analysis.

### 📈 Results & Insights
Here are some key insights from the data cleaning process:

- **Duplicates Removed:** 104 duplicate rows were identified and removed using the ROW_NUMBER() and CTE approach.
- **Missing Addresses:** 35 missing property addresses were filled using the self-join technique based on matching ParcelID.
- **Data Standardization:** The SaleDate column was standardized to remove unnecessary time elements, enhancing the dataset's consistency.
- **Address Splitting:** Concatenated address fields were successfully split into distinct StreetAddress and City columns.
