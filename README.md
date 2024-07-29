# World-Layoff-Data-Cleaning
## Cleaning world layoffs data in MySql

This project focuses on the data cleaning and analysis of world layoffs using MySQL. With an increasing number of layoffs occurring globally, understanding these trends is essential for businesses, policymakers, and researchers.

### Data Cleaning/Preparation

1. Remove Duplicates: Identify and remove duplicate entries to ensure data accuracy and reliability.
2. Standardize Data: Clean and standardize data fields to maintain consistency across the dataset.
3. Handle Missing Values: Use techniques to manage missing or null values effectively.
4. Format Dates: Apply date formatting for uniformity and better analysis.
5. Enhance Data Usability: Transform raw data into a structured format that can be easily analyzed for insights.

```sql
 SELECT *
 FROM layoffs;
 
-- 1. Remove Duplicates
-- 2. Standardize the Data
-- 3. Null Values or blank values
-- 4. Remove Any Columns


 CREATE TABLE layoffs_staging
 LIKE layoffs;
 
 SELECT *
 FROM layoffs_staging;
 
 INSERT layoffs_staging
 SELECT *
 FROM layoffs;
 
 SELECT *,
 ROW_NUMBER() OVER(
 PARTITION BY company, industry, total_laid_off, percentage_laid_off, `date`) AS row_num
 FROM layoffs_staging;
 
 WITH duplicate_cte AS 
 (SELECT *,
 ROW_NUMBER() OVER(
 PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions) AS row_num
 FROM layoffs_staging
 )
 SELECT *
 FROM duplicate_cte
 WHERE row_num > 1;
 
 SELECT *
 FROM layoffs_staging
 WHERE company = 'Casper';
 
 
 CREATE TABLE `layoffs_staging3` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;


SELECT *
FROM layoffs_staging3
WHERE row_num >1;

INSERT INTO layoffs_staging3
SELECT *,
 ROW_NUMBER() OVER(
 PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions) AS row_num
 FROM layoffs_staging;

DELETE 
FROM layoffs_staging3
WHERE row_num >1;

SELECT *
FROM layoffs_staging3
WHERE row_num >1;

SELECT *
FROM layoffs_staging3;


-- Standardizing data

SELECT company, TRIM(company)
FROM layoffs_staging3;

UPDATE layoffs_staging3
SET company = TRIM(company);

SELECT *
FROM layoffs_staging3
WHERE industry LIKE 'Crypto%';

UPDATE layoffs_staging3
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';

SELECT DISTINCT country, TRIM(TRAILING '.' FROM country)
FROM layoffS_staging3
ORDER BY 1;

UPDATE layoffS_staging3
SET country = TRIM(TRAILING '.' FROM country)
WHERE country LIKE 'United States%';

SELECT `date`,
str_to_date(`date`, '%m/%d/%Y')
FROM layoffs_staging3;

UPDATE layoffs_staging3
SET `date` = str_to_date(`date`, '%m/%d/%Y');

SELECT `date`
FROM layoffs_staging3;

ALTER TABLE layoffs_staging3
MODIFY COLUMN `date` DATE;


SELECT *
FROM layoffs_staging3
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

UPDATE layoffs_staging3
SET industry = NULL 
WHERE industry = '';

SELECT DISTINCT industry
FROM layoffs_staging3
WHERE industry IS NULL
OR industry = '';

SELECT *
FROM layoffs_staging3
WHERE company = 'Airbnb';

SELECT *
FROM layoffs_staging3 AS t1
JOIN layoffs_staging3 AS t2
	ON t1.company = t2.company
 WHERE (t1.industry IS NULL OR t1.industry = '');
 
 UPDATE layoffs_staging3 AS t1
 JOIN layoffs_staging3 AS t2
	ON t1.company = t2.company
    SET t1.industry = t2.industry
    WHERE t1.industry IS NULL 
    AND t2.industry IS NOT NULL;
 
 SELECT *
FROM layoffs_staging3
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

DELETE
FROM layoffs_staging3
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

ALTER TABLE layoffs_staging3
DROP COLUMN row_num;

SELECT *
FROM layoffs_staging3;
```



