{\rtf1\ansi\ansicpg1252\cocoartf2761
\cocoatextscaling0\cocoaplatform0{\fonttbl\f0\fswiss\fcharset0 Helvetica;}
{\colortbl;\red255\green255\blue255;}
{\*\expandedcolortbl;;}
\paperw11900\paperh16840\margl1440\margr1440\vieww11520\viewh8400\viewkind0
\pard\tx566\tx1133\tx1700\tx2267\tx2834\tx3401\tx3968\tx4535\tx5102\tx5669\tx6236\tx6803\pardirnatural\partightenfactor0

\f0\fs24 \cf0 # Nashville Housing Data Cleaning\
\
## Introduction\
This project focuses on cleaning and standardizing a Nashville housing dataset with over 56,000 records. The goal is to demonstrate data cleaning techniques that improve data quality and usability, including addressing missing values, standardizing formats, and removing duplicates.\
\
## Objective\
The primary objectives of this project are to:\
- Standardize and clean the dataset for improved usability in analysis.\
- Apply common SQL data cleaning techniques such as handling null values, standardizing date formats, and removing duplicates.\
- Populate missing property addresses based on matching parcel IDs.\
\
## Tools Used\
- **SQL**: For data cleaning and transformation.\
\
## Dataset\
The dataset contains the following key fields:\
- **Parcel ID**: Unique identifier for properties.\
- **Address**: Property address.\
- **Sale Date**: Date the property was sold.\
- **Price**: Sale price of the property.\
\
\
## Key Steps in Data Cleaning\
\
### 1. Removing Duplicates\
The following SQL query was used to remove duplicate rows based on the `ParcelID`, keeping only the most recent sale record:\
\
```sql\
WITH CTE AS (\
    SELECT *,\
        ROW_NUMBER() OVER (PARTITION BY ParcelID ORDER BY SaleDate DESC) AS RowNum\
    FROM NashvilleHousing\
)\
DELETE FROM CTE\
WHERE RowNum > 1;\
```\
- This query partitions the dataset by `ParcelID` and uses `ROW_NUMBER()` to identify duplicate records. Only the most recent record is kept for each property based on the `SaleDate`.\
\
### 2. Standardizing Date Format\
The `SaleDate` column was standardized by removing unnecessary time elements with this query:\
\
```sql\
UPDATE NashvilleHousing\
SET SaleDate = CONVERT(DATE, SaleDate);\
```\
- This ensures that all sale dates are stored in the `DATE` format, improving the consistency of the data.\
\
### 3. Filling Missing Addresses\
Missing property addresses were filled using a self-join technique based on `ParcelID`:\
\
```sql\
UPDATE nh\
SET nh.Address = nh2.Address\
FROM NashvilleHousing nh\
INNER JOIN NashvilleHousing nh2\
ON nh.ParcelID = nh2.ParcelID\
WHERE nh.Address IS NULL AND nh2.Address IS NOT NULL;\
```\
- This query fills in missing address values by matching records that have the same `ParcelID` but a non-null address, ensuring that incomplete records are populated.\
\
### 4. Address Splitting\
Concatenated address fields were split into separate columns using SQL functions like `SUBSTRING` and `CHARINDEX`:\
\
```sql\
SELECT\
    SUBSTRING(Address, 1, CHARINDEX(',', Address) - 1) AS StreetAddress,\
    SUBSTRING(Address, CHARINDEX(',', Address) + 1, LEN(Address)) AS City\
FROM NashvilleHousing;\
```\
- This query extracts the street address and city by splitting the concatenated address field.\
\
\
## Results & Insights\
- **Duplicates Removed**: 104 duplicate rows were identified and removed using the `ROW_NUMBER()` and `CTE` approach.\
- **Missing Addresses**: 35 missing property addresses were filled using a self-join technique based on matching `ParcelID`.\
- **Data Standardization**: The `SaleDate` column was standardized by removing unnecessary time elements, improving the consistency of the dataset.\
- **Address Splitting**: Concatenated address fields were split into separate street address and city columns for better usability.\
\
}