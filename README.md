## Project Overview

This project explores the data analyst job market using SQL. The goal is to identify high-paying jobs, in-demand skills, and the best skills to learn for career growth.

By analyzing job posting data, I answered the following questions:

* What are the highest-paying Data Analyst jobs?
* What skills are required for those jobs?
* What skills are most in demand?
* Which skills offer the highest salaries?
* What are the best skills to learn based on both demand and salary?

---

## Tools Used

### SQL

Used to query and analyze the job market dataset.

### Visual Studio Code

Used for writing and running SQL queries.

### Git & GitHub

Used for version control and sharing the project.

---

# Analysis

## 1. Top-Paying Data Analyst Jobs

To find the highest-paying remote Data Analyst positions, I filtered jobs with available salary information and sorted them by average yearly salary.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim
    ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst'
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

### Key Insight

Remote Data Analyst roles can offer very competitive salaries, especially in large technology and data-focused companies.

---

## 2. Skills Required for Top-Paying Jobs

To understand which skills employers expect from highly paid Data Analysts, I matched the top-paying jobs with their required skills.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst'
        AND job_location = 'Anywhere'
        AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

### Key Insight

Top-paying jobs usually require a combination of SQL, Python, cloud technologies, and business intelligence tools.

---

## 3. Most In-Demand Skills

This query identifies the skills that appear most frequently in remote Data Analyst job postings.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

### Top 5 Most In-Demand Skills

| Skill    | Demand Count |
| -------- | ------------ |
| SQL      | 7,291        |
| Excel    | 4,611        |
| Python   | 4,330        |
| Tableau  | 3,745        |
| Power BI | 2,609        |

### Key Insight

SQL remains the most requested skill for Data Analysts, followed by Excel, Python, Tableau, and Power BI.

---

## 4. Highest-Paying Skills

This analysis calculates the average salary associated with each skill.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;
```

### Key Insight

Skills related to cloud platforms, big data, and programming languages generally offer higher salaries than traditional reporting tools.

---

## 5. Best Skills to Learn

To find the most valuable skills, I combined salary and demand data.

```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills_dim.skill_id
HAVING COUNT(skills_job_dim.job_id) > 10
ORDER BY avg_salary DESC, demand_count DESC
LIMIT 25;
```

### Top Skills Based on Salary and Demand

| Skill      | Demand Count | Average Salary ($) |
| ---------- | ------------ | ------------------ |
| Go         | 27           | 115,320            |
| Confluence | 11           | 114,210            |
| Hadoop     | 22           | 113,193            |
| Snowflake  | 37           | 112,948            |
| Azure      | 34           | 111,225            |
| BigQuery   | 13           | 109,654            |
| AWS        | 32           | 108,317            |
| Java       | 17           | 106,906            |
| SSIS       | 12           | 106,683            |
| Jira       | 20           | 104,918            |



=
