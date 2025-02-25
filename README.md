# Job Market Exploratory Data Analysis

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning](#data-cleaning)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results](#results)



### Project Overview

This data analysis project aims to provide insights into the job market between the years 2020 to 2023. 
By analyzing the data provided for the job market during this time, we seek to find any patterns 
and compare industries and companies against each other. 



### Data Sources

Layoffs Data: The primary dataset used for this analysis is the "layoffs.csv" file, containing information about the layoffs for each company and industry.

### Tools

- MySQL Workbench - Data Cleaning & Exploratory Data Analysis
  - [Download here](https://dev.mysql.com/downloads/installer/)

### Data Cleaning

In the initial data preparation phase which was all done in MySQL Workbench, I performed the following tasks: 

1. Removed all duplicates
2. Standardized data
3. Fixed null/blank values
4. Remove Unnecessary Columns/Rows

### Exploratory Data Analysis

EDA involved exploring layoff data from 2020-2023 to answer key questions below:

- What was the maximum amount of layoffs in a day?
- Which companies no longer exist/went under?
- Which company had the most layoffs?
- Which industry had the most layoffs?
- Which country had the most layoffs?
- What year had the most layoffs?
- Which stage had the most layoffs?
- What year and month had the most layoffs?
- What were the top 5 companies with the most layoffs each year?


### Data Analysis

SQL queries used:

```sql
SELECT Max(total_laid_off), MAX(percentage_laid_off)
FROM layoffs_staging2;


SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;


SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;


SELECT industry, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;


SELECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;


SELECT YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR (`date`)
ORDER BY 1 DESC;


SELECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;


WITH Rolling_Total AS
(SELECT SUBSTRING(`date`,1,7) AS `MONTH`, SUM(total_laid_off) total_off
FROM layoffs_staging2
WHERE SUBSTRING(`date`,1,7) IS NOT NULL
GROUP BY `MONTH`
ORDER BY 1 ASC
)
SELECT `MONTH`, total_off, SUM(total_off) OVER(ORDER BY `MONTH`)
FROM Rolling_Total;


WITH Company_Year (company, years, total_laid_off) AS
(
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
), 
Company_Year_Rank AS
(
SELECT *, DENSE_RANK () OVER (PARTITION BY years ORDER BY total_laid_off DESC) Ranking
FROM Company_Year
WHERE years IS NOT NULL
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <= 5
;

```

### Results

The analysis results are summarized as follows:
1. The maximum amount of lays off in one day was 12,000
2. Over 100 companies went out of business
3. Amazon had the most total layoffs
4. The consumer industry had the most layoffs 
5. The United States had the most layoffs compared to other countries
6. 2022 had the most layoffs
7. Companies in post-ipo stage had the most layoffs
8. January 2023 had the most layoffs
9. Uber, Bytedance, Meta, and Google had the most layoffs during 2020-2023 respectively 
