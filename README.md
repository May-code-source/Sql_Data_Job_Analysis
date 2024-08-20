# Introduction
This project zeroes in on data analyst roles, examining top-paying positions, essential skills, and 📈 the sweet spot where high demand aligns with high salaries in the world of data analytics.

See here [Data_Analysis_Project](/Data_Analysis_Project/)

# Background 
Motivated to better navigate the data analyst job market, this project was developed to identify top-paying positions and the most sought-after skills, simplifying the job search for others.

The dataset provides valuable insights into job titles, salaries, locations, and key skills.

Through my queries, I aimed to solve the following problems:

1. Identifying which data analyst jobs offer the highest salaries.
2. Determining the skills required for these high-paying roles.
3. Uncovering the most in-demand skills for data analysts.
4. Understanding how certain skills correlate with higher salaries.
5. Pinpointing the most valuable skills to learn.

# Tools 
I used several tools for this analysis:

- **SQL**: The core of my analysis, enabling me to query the database and extract crucial insights.
- **PostgreSQL**: The selected database management system, well-suited for handling job posting data.
- **Visual Studio Code**: My primary tool for database management and executing SQL queries.
- **Git & GitHub**: Vital for version control, sharing SQL scripts, and tracking project progress, facilitating collaboration and documentation.

# Analysis 

### 1. Top Paying Data Analyst Jobs
Problem: Identify the top-paying data analyst jobs to provide insights into high-salary opportunities and location flexibility for Data Analysts.I filtered Data Analyst positions based on average yearly salary and location, with a specific focus on remote job opportunities.

```sql
SELECT	
	job_id,
	job_title,
	job_location,
	job_schedule_type,
	salary_year_avg,
	job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND 
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
```
**Findings on Top Data Analyst Jobs in 2023**:

- **Wide Salary Range**: The top 10 highest-paying data analyst roles offer salaries ranging from $184,000 to $650,000, showcasing substantial earning potential in the field.
- **Diverse Employers**: High salaries are offered by a range of companies, including SmartAsset, Meta, and AT&T, highlighting a broad interest across various industries.
- **Variety in Job Titles**: There is a notable diversity in job titles, from Data Analyst to Director of Analytics, reflecting the range of roles and specializations within data analytics.


### 2. Skills for Top-Paying Jobs

To identify the skills required for the highest-paying roles, I combined job postings with skills data. This integration provides a detailed look at which high-paying jobs demand certain skills, helping job seekers understand which skills to develop that align with top salaries

```sql
WITH top_paying_jobs AS (
    SELECT	
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND 
        job_location = 'Anywhere' AND 
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```

**Most In-Demand Skills for Data Analysts in 2023, Based on Job Postings**:

- SQL stands out as the most demanded skill, appearing in 8 job postings.
- Python closely follows, mentioned in 7 job postings.
- Tableau is also highly sought after, with 6 job postings highlighting its importance.
- Other skills such as R, Snowflake, Pandas, and Excel exhibit varying levels of demand.

### 3. In-Demand Skills for Data Analysts
This query pinpointed the skills most commonly requested in job postings, providing insights into the most valuable skills for job seekers

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```
**Findings on the Most In-Demand Skills for Data Analysts in 2023**:

- **SQL and Excel** continue to be crucial, underscoring the importance of strong foundational skills in data processing and spreadsheet management.
- **Programming and Visualization Tools such as Python, Tableau, and Power BI are vital**, highlighting the growing significance of technical expertise in data storytelling and decision-making support.

## Skills Demand Count

| Skill      | Demand Count |
|------------|--------------|
| SQL        | 7291         |
| Excel      | 4611         |
| Python     | 4330         |
| Tableau    | 3745         |
| Power BI   | 2609         |
*Demand Table for the Top 5 Skills in Data Analyst Job Postings*

### 4. Skills and Their Salaries
Analyzing the average salaries for various skills highlighted which skills command the highest pay.

```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```

**Results for Top-Paying Skills for Data Analysts**

- **High Demand for Big Data & Machine Learning Skills:**  
  Analysts with expertise in big data technologies (e.g., PySpark, Couchbase), machine learning tools (e.g., DataRobot, Jupyter), and Python libraries (e.g., Pandas, NumPy) command the highest salaries. This reflects the industry’s premium on advanced data processing and predictive modeling capabilities.

- **Proficiency in Software Development & Deployment:**  
  Skills in development and deployment tools (e.g., GitLab, Kubernetes, Airflow) are highly valued, showing a lucrative overlap between data analysis and engineering. Mastery of these tools, which enable automation and efficient data pipeline management, is associated with higher pay.

- **Expertise in Cloud Computing:**  
  Knowledge of cloud and data engineering tools (e.g., Elasticsearch, Databricks, GCP) underscores the growing importance of cloud-based analytics environments. Proficiency in cloud technologies significantly boosts earning potential in data analytics.

| Skill         | Average Salary ($) |
|---------------|---------------------|
| PySpark       | 208,172             |
| Bitbucket     | 189,155             |
| Couchbase     | 160,515             |
| Watson        | 160,515             |
| DataRobot     | 155,486             |
| GitLab        | 154,500             |
| Swift         | 153,750             |
| Jupyter       | 152,777             |
| Pandas        | 151,821             |
| Elasticsearch | 145,000             |
 *Table of Average Salaries for the Top 10 Paying Skills for Data Analysts* 

### 5. Most Valuable Skills to Acquire
By integrating insights from both demand and salary data, this analysis aimed to identify skills that are highly sought-after and command high salaries, providing strategic insights for career development in data analysis

```sql
SELECT 
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

| Skill ID | Skill       | Demand Count | Average Salary ($) |
|----------|-------------|--------------|---------------------|
| 8        | Go          | 27           | 115,320             |
| 234      | Confluence  | 11           | 114,210             |
| 97       | Hadoop      | 22           | 113,193             |
| 80       | Snowflake   | 37           | 112,948             |
| 74       | Azure       | 34           | 111,225             |
| 77       | BigQuery    | 13           | 109,654             |
| 76       | AWS         | 32           | 108,317             |
| 4        | Java        | 17           | 106,906             |
| 194      | SSIS        | 12           | 106,683             |
| 233      | Jira        | 20           | 104,918             |
*Table of Most Optimal Skills for Data Analysts Sorted by Salary*

### Breakdown of the Most Optimal Skills for Data Analysts in 2023

- **High-Demand Programming Languages:**  
  Python and R are in high demand, with demand counts of 236 and 148, respectively. Despite their widespread use, their average salaries are approximately $101,397 for Python and $100,499 for R, indicating that proficiency in these languages is highly valued but also widely available.

- **Cloud Tools and Technologies:**  
  Skills in specialized technologies such as Snowflake, Azure, AWS, and BigQuery are in significant demand, with relatively high average salaries. This reflects the growing importance of cloud platforms and big data technologies in the field of data analysis.

- **Business Intelligence and Visualization Tools:**  
  Tools like Tableau and Looker are crucial, with demand counts of 230 and 49, and average salaries around $99,288 and $103,795, respectively. This highlights the essential role of data visualization and business intelligence in extracting actionable insights from data.

- **Database Technologies:**  
  The demand for skills in both traditional and NoSQL databases (Oracle, SQL Server, NoSQL) is notable, with average salaries ranging from $97,786 to $104,534. This underscores the continued need for expertise in data storage, retrieval, and management.

# What I Learned

## SQL Skill Enhancement

Throughout this journey, I’ve significantly enhanced my SQL skills with advanced techniques:

- **Advanced Query Techniques:**  
  Mastered crafting complex SQL queries, expertly combining tables and utilizing `WITH` clauses for sophisticated temporary table operations.

- **Data Aggregation Mastery:**  
  Gained proficiency with `GROUP BY` and transformed aggregate functions like `COUNT()` and `AVG()` into essential tools for summarizing data.

- **Analytical Expertise:**  
  Elevated my problem-solving abilities, converting challenging questions into actionable and insightful SQL queries.

# Conclusion 

## Insights

From the analysis, several key insights emerged:

- **Top-Paying Data Analyst Jobs:**  
  The highest-paying remote data analyst positions offer a broad salary range, with some roles reaching up to $650,000!

- **Skills for Top-Paying Jobs:**  
  Advanced proficiency in SQL is crucial for securing high-paying data analyst roles, highlighting its importance for achieving top salaries.

- **Most In-Demand Skills:**  
  SQL stands out as the most sought-after skill in the data analyst job market, making it essential for job seekers.

- **Skills with Higher Salaries:**  
  Specialized skills, such as SVN and Solidity, are associated with the highest average salaries, underscoring the value of niche expertise.

- **Optimal Skills for Job Market Value:**  
  SQL not only leads in demand but also offers high average salaries, making it one of the most valuable skills for data analysts to master in order to maximize their market value.

## Closing

This project enhanced my SQL skills and provided key insights into the data analyst job market. The findings offer guidance on focusing skill development and job search efforts. By targeting high-demand, high-salary skills, aspiring data analysts can improve their position in a competitive field. This work highlights the need for continuous learning and staying updated with industry trends.


