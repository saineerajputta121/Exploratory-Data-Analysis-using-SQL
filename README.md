# 📊 Exploratory Data Analysis (EDA) using SQL

## 📄 Project Description

In this project, I performed **Exploratory Data Analysis (EDA)** on a cleaned layoffs dataset using **MySQL**. The aim was to uncover trends, patterns, and insights related to employee layoffs across industries, companies, and time periods.

The data was originally sourced from [Kaggle] and cleaned prior to this step. This project focuses on using **SQL** to explore and analyze that cleaned dataset.

---

## 🛠️ Tools Used

- **MySQL**
- **MySQL Workbench**
- **Kaggle** (Dataset Source)
- **GitHub** (Version Control)

---

## 🔍 EDA Workflow & Key Insights

### 1. General Statistics  
Calculate minimum, maximum, and average layoffs to understand the data range and central tendency.

```sql
SELECT MIN(total_laid_off) AS min_laid_off, 
       MAX(total_laid_off) AS max_laid_off, 
       AVG(total_laid_off) AS avg_laid_off 
FROM layoffs2;
```

### 2. Identify Companies with Specific Layoffs  
Find companies with the smallest and largest layoff counts to spot outliers or notable cases.

```sql
Companies with the smallest layoffs (e.g., total_laid_off = 3)
SELECT company 
FROM layoffs2 
WHERE total_laid_off = 3 
GROUP BY company;

Details for a specific company (example: 'branch')
SELECT * FROM layoffs2 WHERE company = 'branch';

 Companies with the largest layoffs (e.g., total_laid_off = 12000)
SELECT company 
FROM layoffs2 
WHERE total_laid_off = 12000 
GROUP BY company;

 Details for a specific company (example: 'clearco')
SELECT * FROM layoffs2 WHERE company = 'clearco';
```

### ✅ 3. Below-Average Layoffs  
Companies that laid off fewer people than the data average.

```sql
SELECT * 
FROM layoffs2
WHERE total_laid_off <= (
    SELECT AVG(total_laid_off) FROM layoffs2
)
ORDER BY company;
```

---

### ✅ 4. Monthly Layoff Trend  
layoffs over time using monthly aggregations.

```sql
WITH rolling AS (
    SELECT 
        DATE_FORMAT(date, '%Y-%m') AS month,
        SUM(total_laid_off) AS total_off
    FROM layoffs2
    WHERE date IS NOT NULL
    GROUP BY month
)
SELECT 
    month,
    total_off,
    SUM(total_off) OVER (ORDER BY month) AS cumulative_total
FROM rolling;
```

---

### ✅ 5. Top 5 Companies by Layoffs per Year  
Use of `DENSE_RANK()` to rank companies by layoff volume annually.

```sql
WITH yearly_totals AS (
    SELECT 
        company,
        YEAR(date) AS year,
        SUM(total_laid_off) AS total_off
    FROM layoffs2
    GROUP BY company, year
),
ranked_companies AS (
    SELECT *,
           DENSE_RANK() OVER (PARTITION BY year ORDER BY total_off DESC) AS rank
    FROM yearly_totals
)
SELECT * 
FROM ranked_companies 
WHERE rank <= 5;
```
## 📊 Conclusion

Through this Exploratory Data Analysis, I have uncovered key insights into employee layoffs across companies, industries, and time. By computing statistics, identifying trends, and ranking high-impact companies and developed a clear understanding of:

- The distribution and scale of layoffs across the dataset  
- Companies with the highest and lowest number of layoffs  
- Time-based patterns such as spikes in specific months  
- Top contributors to workforce reductions in each year

These findings can be used to inform further analysis, develop dashboards, or even support predictive modeling in future projects. The cleaned and explored dataset is now well-suited for visualization tools like **Power BI**, **Tableau**, or extended **SQL-based reporting**.





