# 📊 Data Analyst Job Market Analysis

**Navigate the data job market with data-driven insights.** This project performs a deep dive into the data analyst job market using SQL. By analyzing job postings, salaries, and skill requirements, it identifies top-paying roles and the optimal skills to learn for maximum career value.

**Original Data Source:** [SQL Course by Luke Barousse](https://lukebarousse.com/sql)

## 🛠️ Tools Used

* **SQL:** The backbone of the analysis for querying the database.
* **PostgreSQL:** Database management system used to handle job posting data.
* **Visual Studio Code:** Tool for database management and query execution.
* **Git & GitHub:** For version control and sharing the analysis.

---

## 🔍 The Analysis

### 1. Top Paying Data Analyst Jobs

**Goal:** Identify the highest-paying remote Data Analyst roles to determine the salary ceiling in the field.

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

**💡 Insight:** The top remote roles command salaries between **$184k and $650k**, offered by diverse tech giants like Meta and AT&T. This reveals a massive earning potential for specialized remote analysts.

### 2. Skills for Top Paying Jobs

**Goal:** Pinpoint the specific skills required for the top 10 highest-paying jobs identified above to understand what employers value most.

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

**💡 Insight:** High salary doesn't always mean obscure skills. **SQL (8/10)** and **Python (7/10)** are the most recurring skills in top-tier jobs, followed by **Tableau**. Mastery of foundational tools is crucial even at the highest levels.

### 3. Most In-Demand Skills

**Goal:** Determine which skills appear most frequently in job postings to prioritize learning.

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

**💡 Insight:**
| Skill | Demand Count |
| :--- | :--- |
| **SQL** | 7,291 |
| **Excel** | 4,611 |
| **Python** | 4,330 |

**SQL is king.** It is the most requested skill, beating out Excel and Python.

### 4. Skills Based on Salary

**Goal:** Identify which specific technical skills are associated with the highest average salaries.

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

**💡 Insight:** Niche skills pay more. Tools like **PySpark ($208k)**, **Bitbucket ($189k)**, and **Couchbase ($160k)** top the list. This indicates a premium on Big Data, Machine Learning, and Cloud Engineering skills.

### 5. Most Optimal Skills to Learn

**Goal:** Find the "sweet spot" skills that are both widely requested (>10 jobs) and highly paid.

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

**💡 Insight:**

* **Cloud is Crucial:** Snowflake, Azure, AWS, and BigQuery all appear with salaries >$108k.
* **The Big Three:** Python, Tableau, and SQL remain the safest bets for a balance of job security and decent pay (~$100k avg).

---

## 🧠 What I Learned

Throughout this project, I advanced my SQL capabilities from basic queries to complex analytics:

* **Advanced Querying:** Mastered CTEs (Common Table Expressions) and complex JOINS.
* **Aggregation Strategy:** Utilized `GROUP BY` and `HAVING` to summarize large datasets effectively.
* **Real-world Application:** Translated abstract business questions into executable SQL logic.

## 🚀 Conclusion

This analysis confirms that while specialized skills (Big Data/Cloud) offer the highest pay, the core trifecta of **SQL, Excel, and Python** remains the most critical for entry and stability in the market. The optimal path for a Data Analyst is to master SQL and Python first, then specialize in a Cloud platform (AWS/Azure/Snowflake) to maximize earning potential.

### 📬 Author

[**Mohamed Elmustafa Ahmed Omer Osman**](https://github.com/mohmusahm2000)