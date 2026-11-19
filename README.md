# 📊 Developer Technology Trends: Stack Overflow Survey Analysis
### IBM Data Analyst Professional Certificate – Capstone Project

Analysis of **65,437 responses** to the **Stack Overflow Developer Survey**, combined with job-posting data (REST API) and salary data (web scraping), to identify which technologies developers use **today** and which are gaining interest for **next year**.

> Capstone project of the [IBM Data Analyst Professional Certificate](https://www.coursera.org/professional-certificates/ibm-data-analyst) (Coursera).
> 📄 **Full presentation:** [Developer_Technology_Trends_Presentation.pdf](presentation/Developer_Technology_Trends_Presentation.pdf)

---

## 🎯 Business Questions
- Which programming languages, databases, platforms and web frameworks are used **now** and wanted **next year**?
- Which technologies have the most job postings and the highest salaries?
- Who are the respondents (age, education, country)?

**Audience:** IT managers, recruiters, training providers and developers planning their skills.

## 💡 Key Findings
- **Languages:** JavaScript, HTML/CSS, Python and SQL are used most today. **Python becomes the #1 most wanted language** for next year (25,047 respondents); Rust and Go enter the top 10, while Java and C drop out.
- **Databases:** **PostgreSQL leads** both current use (25,536) and future interest (24,005). MySQL and SQL Server lose about 40% of interest; Supabase enters the top 10.
- **Cloud & web:** AWS (22,191), Azure and Google Cloud dominate; Node.js and React lead web frameworks, with React overtaking Node.js next year.
- **Salaries:** Swift ($130,801), Python ($114,383) and C++ ($113,865) are the best-paid languages.
- **Demographics:** the typical respondent is 25–44 years old (25–34 ≈ 37%), holds a degree and works remotely or hybrid; the USA, Germany, India and the UK have the most respondents.

**Recommendation:** prioritise upskilling in **Python, SQL/PostgreSQL and cloud**, and track **Rust, Go and TypeScript** as next-wave skills.

## 🛠️ Tools & Technologies
| Stage | Tools |
|---|---|
| Data collection | Python, `requests` (REST API), `BeautifulSoup` (web scraping), `openpyxl` |
| Data wrangling | Python, Pandas |
| Exploratory analysis & visualization | Pandas, Matplotlib |
| Dashboard | Power BI |
| Presentation | PowerPoint |

## 📁 Repository Structure
```
├── data/            # job postings (xlsx) and salaries (csv)
├── notebooks/       # data collection and exploration notebooks
├── images/          # charts and screenshots used in this README
├── presentation/    # final presentation (PDF)
└── README.md
```

---

## 🔄 Project Workflow

### 1. Data Collection

#### 1a. Job postings via REST API
**Tools:** Python, `requests`, `json`, `openpyxl`

**Goal:** determine how many jobs are currently open for various technologies and in various US locations.

**What I did:**
- Retrieved a JSON dataset of job postings from a REST API endpoint (fields: *Job Title, Job Experience Required, Key Skills, Role Category, Location, Functional Area, Industry, Role*).
- Wrote two reusable functions:
  - `get_number_of_jobs_T(technology)` counts postings that list a given technology in *Key Skills*
  - `get_number_of_jobs_L(location)` counts postings for a given city
- Tested them: **Python → 1,173 postings**, **Los Angeles → 640 postings**.
- Looped over 7 cities (Los Angeles, New York, San Francisco, Washington DC, Seattle, Austin, Detroit) and 12 technologies (C, C#, C++, Java, JavaScript, Python, Scala, Oracle, SQL Server, MySQL Server, PostgreSQL, MongoDB) and exported the results to Excel with `openpyxl`: `job-postings.xlsx` and `job-postings-technology.xlsx`.

![Function counting jobs per technology](images/01a_api_function_technology.png)
![Function counting jobs per location](images/01b_api_function_location.png)
![Exporting results to Excel](images/01c_api_export_excel.png)

**Results:**

| Location | Jobs | | Technology | Jobs |
|---|---:|---|---|---:|
| Washington DC | 5,316 | | Java* | 3,428 |
| Detroit | 3,945 | | JavaScript | 2,248 |
| Seattle | 3,375 | | Python | 1,173 |
| New York | 3,226 | | Oracle | 899 |
| Los Angeles | 640 | | C# | 526 |
| San Francisco | 435 | | C++ | 506 |
| Austin | 434 | | SQL Server | 423 |
| | | | MongoDB | 208 |
| | | | Scala | 138 |
| | | | PostgreSQL | 86 |

![Results loaded back from Excel](images/01d_api_results.png)

> **⚠️ Data quality note:** the course-provided counting method uses pattern/substring matching, which inflates results for short names: **"C" returned 25,114 postings** because the pattern matches any capital letter *C* in the skills text, and **"Java" also counts "JavaScript"** postings. **"MySQL Server" returned 0**, which suggests the dataset uses a different label (e.g. *MySQL*). A more reliable approach is to split *Key Skills* into separate items and match exact names.

#### 1b. Web scraping
**Tools:** Python, `requests`, `BeautifulSoup`, Pandas

**Goal:** extract the name and average annual salary of popular programming languages from a web page and save them to a CSV file.

**What I did:**
- Downloaded the HTML page with `requests` and parsed it with `BeautifulSoup` (`html.parser`).
- Located the table, iterated over its rows (`tr`) and cells (`td`), skipping the header row.
- Cleaned the salary values (removed `$` and thousand separators → integers) so they can be used in calculations.
- Saved the result as `popular-languages.csv`.

![Web scraping – setup](images/01e_scraping_setup.png)
![Web scraping – results](images/01f_scraping_results.png)

**Results – average annual salary by language:**

| Language | Avg. annual salary (USD) |
|---|---:|
| Swift | 130,801 |
| Python | 114,383 |
| C++ | 113,865 |
| JavaScript | 110,981 |
| Java | 101,013 |
| Go | 94,082 |
| R | 92,037 |
| C# | 88,726 |
| SQL | 84,793 |
| PHP | 84,727 |

### 2. Data Wrangling
**Tools:** Python, Pandas

- Dataset: Stack Overflow Developer Survey – **65,457 rows × 114 columns**.
- Removed **20 duplicate rows** → 65,437 unique responses.
- Handled missing values and standardised text values.
- Split multi-answer columns (e.g. `LanguageHaveWorkedWith`) on `;` to count each technology separately.
- Mapped age ranges to numeric values to calculate summary statistics.

**Dataset profile** ([notebook](notebooks/03_exploring_dataset.ipynb)):
- **Mean age ≈ 32.7 years** (age ranges mapped to midpoints)
- Respondents from **185 countries**
- **Median annual compensation: $65,000**
- **80%** of respondents who answered the work-mode question work hybrid (23,015) or fully remote (20,831)

![First rows of the dataset](images/eda_01_head.png)
![Rows and columns](images/eda_02_shape.png)
![Mean age and number of countries](images/eda_03_age_countries.png)

### 3. Exploratory Analysis & Visualization
**Tools:** Pandas, Matplotlib

#### Job market
![Job postings by technology](images/02_job_postings_chart.png)
![Average annual salary by language](images/03_salary_chart.png)

#### Programming languages – current year vs next year
JavaScript, HTML/CSS, Python and SQL dominate today. Python becomes #1 for next year, Rust and Go enter the top 10, Java and C drop out.

| Current year | Next year |
|---|---|
| ![Languages used](images/04_languages_current.png) | ![Languages wanted](images/05_languages_next.png) |

#### Databases – current year vs next year
PostgreSQL leads now and next year. MySQL and SQL Server lose about 40% of their interest, Oracle drops out and Supabase enters the top 10.

| Current year | Next year |
|---|---|
| ![Databases used](images/06_databases_current.png) | ![Databases wanted](images/07_databases_next.png) |

#### Demographics
| Age groups | Top 10 countries |
|---|---|
| ![Respondents by age](images/11_age_groups.png) | ![Respondents by country](images/12_top_countries.png) |

### 4. Interactive Dashboard
**Tool:** Power BI – three tabs:

**Tab 1 – Current Technology Usage:** most used language JavaScript (37,492), most used databases PostgreSQL and MySQL, AWS leads platforms, Node.js and React lead web frameworks.
![Dashboard – current technology usage](images/08_dashboard_current.png)

**Tab 2 – Future Technology Trends:** Python becomes the most wanted language, PostgreSQL stays the most wanted database, React overtakes Node.js; Svelte, Spring Boot and Django enter the top 10.
![Dashboard – future technology trends](images/09_dashboard_future.png)

**Tab 3 – Demographics:** 25–34 is the largest age group (23,911); USA has the most respondents (11,095); bachelor's (24,942) and master's (15,557) degrees dominate.
![Dashboard – demographics](images/10_dashboard_demographics.png)

### 5. Presentation of Findings
Final storytelling presentation (executive summary, methodology, results, findings & implications, conclusion):
👉 [Developer_Technology_Trends_Presentation.pdf](presentation/Developer_Technology_Trends_Presentation.pdf)

---

## 🔍 Conclusion
- Core stack for today and tomorrow: **JavaScript/TypeScript, Python, SQL and PostgreSQL**.
- **Python** is the best single skill to invest in: the most wanted language and among the best paid.
- Rising technologies (**Rust, Go, Supabase, Svelte**) deserve monitoring and pilot projects.
- **Limitation:** respondents are mostly early- and mid-career developers, so the trends mainly reflect that group.
- **Next step:** repeat the analysis on future survey editions to confirm these trends.

## 👤 Author
**Jarosław Baran** – Data Analyst
[GitHub](https://github.com/JaroslawBaran89)
