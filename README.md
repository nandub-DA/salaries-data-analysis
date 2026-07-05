# PAYROLL DEDUCTION BURDEN ANALYSIS, MEXICO 

## 1. PROJECT OVERVIEW

This project analyzes payroll records from Mexico to examine how payroll deduction burdens vary across different workforce segments. The analysis is based on approximately 1.978 million payroll transactions covering 31 states, 2565 organizations, 91411 departments, and 72833 job roles.

The study investigates payroll deduction patterns across salary bands, geographic regions, organizations, departments, and job roles using median deduction rates as the primary measure of deduction burden. Additional analysis explores whether deduction rate differences persist across states after accounting for salary level.

The objective of the project is to identify workforce segments that experience disproportionately high payroll deduction burdens and to understand how salary level, geographic location, organizational affiliation, and occupational role relate to payroll deduction outcomes.

## 2. TOOLS USED

* Power query
* Power BI

## 3. DATASET

* Source: https://www.kaggle.com/datasets/ivansabik/mexican-federal-government-salaries
* Data contains: STATE, ORGANISATION, DEPARTMENT, JOB_TITLE, PERSON_NAME, NET_SALARY, GROSS_SALARY, REPORTING_PERIOD_START, REPORTING_PERIOD_ENDS, INFORMATION_ID

## 4. STEPS FOLLOWED

### CLEANED AND TRANSFORMED DATA

* Translated the column names from Spanish (Mexico) to English.
* There is 72833 categories in job titles column alone. More than 1177771 distinct names. 2565 distinct organisations. And same way 91411 departments too. In state column there are 31 states. Totally there is more than 1978156 transactions that is more than 19.78 lakh rows.
* Trimmed extra spaces, removed non-printable characters, and converted text to uppercase.
* Removed line breaks from text fields.
* Set correct data types for all columns. Used Spanish (Mexico) for dates (date locale).
* Checked for errors.
* Did not remove duplicates because INFORMATION_ID is unique for every row.
* Removed the DESIGNATION column because it contained the same values as the JOB_TITLE column.
* Removed rows where gross salary was missing or zero, as they have no financial impact.
  Net salary rows still contain nulls and zero, but these were kept because gross salary was present.
  Net salary values were not filled or changed, as doing so would break the logical relationship between gross and net salary.
* Filled missing and 'EN BLANCO' person names with NAME UNKNOWN.
* Removed Blank Date rows.
* The same employee name appears multiple times in the dataset. These records were not treated as duplicates because employees may have multiple payroll transactions across different reporting periods or changes in payroll-related information. The dataset does not provide sufficient documentation to determine the exact reason for each repeated record.
* The reporting periods are not consistent across the dataset. Records span multiple reporting period start and end dates, and the reporting years also vary. Therefore, the data does not represent a single common reporting period for all employees.
* The dataset contained several date quality issues in the reporting period start and end date columns. Some records had unrealistic years such as 0208, 0219, 1941 and 2094. There were also records where the reporting period end date occurred before the start date.
To identify the actual operational payroll period, the distribution of years in both date columns was analyzed. More than 99.99% of records belonged to the years 2015 to 2020, with 2018 and 2019 contributing the majority of payroll transactions.
Based on this analysis, a DATE_VALIDITY_STATUS was created using the following conditions:
Reporting period start date must be before or equal to reporting period end date.
Both start year and end year must belong to the range 2015 to 2020.
Records not satisfying these conditions were marked as INVALID and excluded from duration-based calculations such as number of days worked, salary per day, and deduction analysis.
This validation process helped improve the reliability of payroll and compensation analysis by removing corrupted or unrealistic reporting periods.
* The dataset contains highly fragmented job title and department categories, likely caused by inconsistent naming conventions across organizations and data quality issues. This may slightly affect the precision of workforce grouping and category-level payroll analysis.
* Created GROSS_SALARY_FLAG column. Only payroll records with positive gross salary values were included in analysis.
Records containing zero or negative gross salary values were treated as invalid for salary distribution and deduction analysis because no supporting business documentation was available to explain whether these values represented adjustments, reversals, inactive payroll records, or other non-standard payroll events.
To avoid introducing unsupported assumptions into the analysis, only positive gross salary records were used for salary normalization and compensation-related calculations.
* A NET_GREATER_THAN_GROSS flag column was created to identify records where net salary was greater than gross salary.
In normal payroll calculations, net salary should not exceed gross salary because deductions are usually subtracted from gross salary to calculate take-home pay.
Records where net salary was greater than gross salary were marked as "INVALID" and excluded from salary-per-day and deduction analysis to avoid distorted compensation calculations.
The validation was applied only to rows where net salary values were available.
* A NET_SALARY_FLAG column was created to identify records suitable for analysis.
Records were marked as "VALID" only when:
Net salary was not blank
Net salary was greater than or equal to 0
GROSS_SALARY_FLAG already marked as valid
Zero net salary values were retained when the corresponding gross salary was positive because these records may represent cases where deductions or recoveries reduced the employee's take-home salary to zero.
Blank net salary values were treated as invalid because they represent missing or unavailable payroll information rather than actual compensation values.
Negative net salary values were also treated as invalid for compensation analysis due to the absence of supporting business documentation explaining these records.
* Job title values contained many numbers and mixed formats. Only missing values were filled with UNKNOWN JOB TITLE.
* Department values also contained many mixed formats. Only missing values were filled with UNKNOWN DEPARTMENT.
* Person name, department, and job title were not heavily cleaned because there was no official reference to standardise them. Only blanks were handled.
* Payroll reporting periods vary across records. To standardize salary comparisons, gross salary and net salary were converted into daily values in GROSS_SALARY_PER_DAY and NET_SALARY_PER_DAY columns using the NUMBER_OF_DAYS_WORKED column, which was calculated from the reporting period start and end dates. This ensures salaries from different payroll durations can be compared on a consistent daily basis. Records with zero or negative values were excluded from the daily salary calculation by returning BLANK().
* A GROSS_SALARY_PER_DAY_BAND column was created by grouping GROSS_SALARY_PER_DAY into predefined salary ranges. This simplifies the analysis of salary distribution and enables comparison of payroll deduction rates across different gross salary groups. Blank values remained blank.
* A NET_SALARY_PER_DAY_BAND column was created by grouping NET_SALARY_PER_DAY into predefined salary ranges. This simplifies the analysis of salary distribution and enables comparisons of payroll deductions across different net salary groups. Zero net salary values were assigned to a separate category, while blank values remained blank.
* A DEDUCTIONS column was created by calculating the difference between GROSS_SALARY_PER_DAY and NET_SALARY_PER_DAY. Invalid or incomplete records returned BLANK().
* A DEDUCTION_BAND column was created by grouping deduction amounts into predefined ranges. This simplifies the analysis of payroll deduction distribution and enables comparison of employees based on deduction levels. Zero deductions were assigned to a separate category, while blank values remained blank.
* A DEDUCTION_RATE column was created by dividing DEDUCTIONS by GROSS_SALARY_PER_DAY and multiplying the result by 100 to express deductions as a percentage of gross salary.

### EXPLORATORY DATA ANALYSIS

#### UNIVARIATE ANALYSIS

* ORGANISATIONS
1. The dataset contains payroll transactions from 2,565 different organizations spread across 31 Mexican states.
2. Payroll transactions are not evenly distributed across organizations. A small number of organizations contribute a large share of total transactions.
3. The top 10 organizations alone contribute around 36.65% of all payroll transactions.
4. Nearly half of all payroll transactions come from the top 20 organizations, showing that payroll activity is concentrated among a limited number of large organizations.
5. Along with a few very large organizations, the dataset also contains thousands of smaller organizations with low transaction activity.
6. The distribution suggests a mixed organizational structure with both high-volume institutions and many smaller entities.
7. Most of the high-transaction organizations appear related to education, healthcare, administration, and public services.
8. The sharp drop in transaction volume after the top organizations shows a long-tail organizational structure where many organizations contribute only a small portion of total payroll activity.

* STATES
1. Payroll transactions are highly concentrated in a small number of states rather than being evenly distributed across Mexico.
2. The top 5 states alone contribute around 70.73% of all payroll transactions.
3. The top 10 states contribute around 86.01% of total transactions, showing that most payroll activity is concentrated in a limited number of regions.
4. In contrast, the bottom 10 states together contribute only around 1.59% of total payroll transactions.
5. The bottom 15 states contribute less than 4% of total transactions, indicating extremely low payroll activity in many regions.
6. The sharp drop in transaction share after the top states suggests that workforce and payroll operations are heavily centralized in a few regions.
7. Many states have very small payroll transaction volumes, which may indicate smaller workforce size, fewer participating organizations, or lower operational activity.

* NET SALARY
1. The largest employee concentrations are:
10K–25K, 5K–10K, 25K–50K

* JOB TITLES
1. Payroll transactions are moderately concentrated among a small number of job roles. The top 10 roles alone contribute nearly 19% of all transactions.
2. Although the dataset contains more than 72,000 distinct job titles, only a small number of roles account for a large share of payroll activity.
3. The top 20 job roles contribute more than one-fourth of total payroll transactions, indicating the presence of dominant operational workforce categories.
4. Beyond the most common job roles, transaction volume becomes highly fragmented across thousands of low-frequency titles.
5. The payroll structure appears to combine a stable core workforce with a large number of specialized or inconsistently named roles.

* DEPARTMENTS
1. The dataset contains a very large number of departments, with more than 91,000 unique department names.
2. The top 10 departments together contribute only around 6.05% of all payroll transactions.
3. Most departments contribute only a very small share of total payroll transactions.
4. The workforce structure appears highly decentralized at the department level.
5. The very high number of unique departments suggests inconsistent naming standards, duplicate department naming, or highly detailed departmental classifications.
9. Similar departments may exist under slightly different names, abbreviations, or organizational formats. The dataset likely combines payroll records from many independent organizational structures with different department naming systems.
10. Compared to organizations and states, department-level payroll activity is far more fragmented.

* INFORMATION ID
1. Unique for each transaction
2. No missing values

* REPORTING PERIOD START & REPORTING PERIOD ENDS
1. There are 437 distinct values in REPORTING PERIOD START and 488 distinct values in REPORTING PERIOD ENDS
2. The dataset contains a mix of standardized reporting periods and shorter employment periods, including records spanning only a single day.
3. Almost all payroll records fall within the reporting years 2015–2020, accounting for 99.99% of both reporting period start and end dates. Payroll activity is heavily concentrated in 2018 (approximately 46%) and 2019 (approximately 41%), followed by 2020 (approximately 12%), while records from 2015–2017 together represent less than 1% of the dataset.
4. Most reporting period start dates occur in January, July, April, and October, indicating strong alignment with quarterly reporting cycles.
5. Most reporting period end dates occur in June, December, March, and September, suggesting that payroll records are frequently reported at quarter-end and half-year-end boundaries.
6. Approximately 88% of reporting period start dates fall in the four quarter-start months, while approximately 85% of reporting period end dates fall in the four quarter-end months.
7. The date distribution indicates that payroll reporting follows highly standardized administrative cycles rather than completely random reporting periods.
8. Although quarterly and semi-annual reporting periods dominate the dataset, payroll transactions are present in all twelve months, indicating the coexistence of standard reporting cycles and employee-specific payroll periods.
9. The concentration of transactions around quarter boundaries suggests that many payroll records may represent reporting periods rather than actual employment start and end dates.

* NUMBER_OF_DAYS_WORKED
1. There are 191 distinct values
2. Semi-annual reporting periods and Quarterly reporting periods dominate the dataset. The middle 50% of payroll records span between roughly three months and six months, confirming that quarterly and semi-annual periods form the core reporting structure.
Q1 = 90 days
Q3 = 181 days
IQR = 91 days
The most common period lengths are:
181 days- 15.88%
184 days- 14.24%
182 days- 3.68%
92 days- 18.04%
91 days- 15.71%
90 days- 9.40%
Together these represent around 77% of transactions.
3. The payroll system appears to use a mix of reporting cycles
Common durations include:
15 days
30 days
31 days
90–92 days
181–184 days
365 days
The overall duration range extends from 1 day to 1186 days.
The dataset contains a combination of:
biweekly reporting periods
monthly reporting periods
quarterly reporting periods
semi-annual reporting periods
annual reporting periods
This indicates that organizations follow multiple payroll reporting schedules.
4. Need for Salary Normalization
The large variation in reporting period length indicates that payroll transactions do not follow a single standardized duration. As a result, direct comparison of gross salary and net salary values would be misleading, which justifies the use of daily salary normalization for compensation analysis.
5. Duration distribution is right-skewed
Mean = 118.36 days
Median = 92 days
Mean > Median
Maximum duration is much larger than the most common durations. Most payroll records have relatively short to medium reporting periods, while a smaller number of long-duration records create a right-skewed distribution.

* NET_GREATER_THAN_GROSS 
1. 94.94% are valid and 5.06% are invalid

* GROSS SALARY FLAG
1. 99.89% are valid and only 0.11% are invalid

* NET SALARY FLAG 
1. 97.29% are valid and 2.71% are invalid

* DATE VALIDITY STATUS
1. 99.92% of payroll records were classified as valid. Only 0.08% of records were marked as invalid.
2. Date-related data quality issues are rare and have minimal impact on the overall dataset. Excluding these records is unlikely to materially affect analytical results.

* NET SALARY PER DAY
1. Most payroll records belong to the lowest salary band. 93.88% of records fall in the ≤500 per day band. Only 6.12% of records are above 500 per day.
The payroll population is heavily concentrated in lower daily salary ranges. High daily salary records exist but represent a very small portion of the workforce.
2. The typical daily net salary is relatively low and most employees are clustered within a narrow salary range.
Median = 99.39 per day
Q1 = 54.15 per day
Q3 = 188.13 per day
IQR = 133.98
50% of all payroll records have daily net salaries between approximately 54 and 188 per day. A typical employee receives less than 100 per day in net compensation.
3. The salary distribution is strongly right-skewed
Mean = 183.35
Median = 99.39
Maximum = 131,513.11
The average daily salary is substantially higher than the median because a small number of very high salary records pull the average upward. The mean does not represent the typical employee well in this dataset. Extreme high-salary records are present.
4. Salary variation for a typical employee is moderate
Median Absolute Deviation = 60.07
A typical payroll record differs from the median daily salary by about 60 per day. This suggests that most employees earn salaries reasonably close to the typical compensation level, despite the presence of extreme high-salary records.
This is a more representative measure of spread than standard deviation because the distribution is heavily skewed.
5. Very high salary bands represent a tiny fraction of records
0.10% between 2,501–5,000
0.10% between 5,001–10,000
0.05% between 10,001–25,000
0.01% between 25,001–50,000
0.003% above 50,000
Very high daily salaries are extremely rare. The payroll structure is dominated by lower and middle salary levels, while high earners form only a small tail of the distribution.
6. Zero-salary records are uncommon
1.23% of records have a net salary per day of zero.
Zero net salary records represent a small portion of the dataset. These may reflect cases where deductions or recoveries reduced take-home pay to zero, or other special payroll situations or data quality issue.
7. The dataset contains substantial salary diversity
680,956 distinct daily salary values
The large number of unique salary values indicates a highly granular compensation structure rather than a small number of standardized pay rates.

* GROS SALARY PER DAY
1. Most payroll records belong to the lower salary bands. 91.70% of payroll records fall within the ≤500 per day band. Only 8.30% of records exceed 500 per day.
The workforce is heavily concentrated in lower daily gross salary levels. Higher salary bands exist but account for only a small proportion of payroll records.
2. The typical gross salary is relatively low
Median = 122.46 per day
A typical payroll record has a daily gross salary of approximately 122 per day. Since the distribution is highly skewed, the median provides a better representation of the typical employee than the average.
3. Half of all payroll records fall within a moderate salary range
Q1 = 66.88
Q3 = 232.35
IQR = 165.48
The middle 50% of payroll records have daily gross salaries between approximately 67 and 232 per day. This indicates that most employees receive compensation within a relatively concentrated range.
4. Typical salary variation is moderate
MAD = 66.96
A typical payroll record differs from the median gross salary by about 67 per day. This suggests that most employees earn salaries reasonably close to the typical compensation level despite the presence of a few extremely high salary records.
5. The distribution is strongly right-skewed. Extreme high-salary records are present
Mean = 229.71
Median = 122.46
Maximum = 189,415 per day
Range = 189,414.94
The average daily gross salary is almost twice the median. This indicates that a relatively small number of high-salary records significantly increase the overall average while most employees earn substantially less.
7. Compensation levels are highly diverse
456,534 distinct daily gross salary values.
The large number of unique salary values indicates a highly granular compensation structure with substantial variation across employees, organizations, departments, and job roles.
8. High salary bands contain very few records
0.37% of records have daily gross salaries above 2,500.
0.17% exceed 5,000 per day.
Only 0.02% exceed 25,000 per day.
Very high compensation levels are extremely uncommon. The payroll system is overwhelmingly dominated by lower and middle salary ranges.

* DEDUCTIONS
1. Most payroll records have relatively small deductions. 
70.71% of payroll records have deductions below 50 per day. 84.43% have deductions below 100 per day.
Deductions are concentrated in the lower bands
23.43% fall in the 0–10 band.
26.37% fall in the 10–25 band.
20.91% fall in the 25–50 band.
The majority of employees experience relatively small daily deductions. Large deduction amounts exist but are concentrated in a small portion of payroll records.
2. The typical deduction is modest
Median = 23.41
Q1 = 9.59
Q3 = 53.00
A typical payroll record has a daily deduction of about 23. Most employees experience deductions well below 100 per day.
3. Half of all payroll records have deductions between 9.59 and 53
IQR = 43.41
The middle 50% of payroll records have deductions between approximately 10 and 53 per day, indicating that deduction amounts are fairly concentrated around lower values.
4. Typical variation in deductions is relatively small
MAD = 19.06
A typical payroll record differs from the median deduction by about 19 per day. This suggests that most employees experience deduction amounts reasonably close to the typical level.
This is a more representative measure of spread than the range because the distribution contains extreme values.
5. The deduction distribution is strongly right-skewed. Extreme deduction values exist
Mean = 56.84
Median = 23.41
Maximum deduction = 119,670.45
Range = 119,670.45
The average deduction is more than twice the median deduction, indicating that a relatively small number of records with large deductions pull the average upward.
The mean does not represent the typical employee particularly well.
7. Zero deductions are relatively uncommon
Only 2.72% of payroll records have zero deductions.
Most employees experience some level of payroll deduction. Records with no deductions represent a small minority of payroll transactions.
8. High deduction amounts are rare
0.69% of records have deductions between 500 and 1000. Only 0.26% exceed 1000.
Very large deductions occur in only a tiny fraction of payroll records. The deduction structure is dominated by low and moderate deduction amounts.
9. Deduction amounts are highly granular
668,119 distinct deduction values.

#### BIVARIATE ANALYSIS

* STATES × MEDIAN DEDUCTION RATE

Deduction burden varies significantly across states.

Highest deduction burden states:
Oaxaca = 33.96%
Jalisco = 31.04%
Tlaxcala = 26.52%
Durango = 25.26%
Mexico = 24.48%

Lowest deduction burden states:
Sinaloa = 5.14%
Hidalgo = 6.30%
Guanajuato = 12.57%
Colima = 13.43%
Queretaro = 13.72%

Oaxaca's median deduction rate is 33.96%.
Sinaloa's median deduction rate is 5.14%.

Employees in Oaxaca experience a typical deduction burden that is more than six times higher than employees in Sinaloa. This suggests substantial regional differences in payroll structures, compensation policies, or  deductions. Deduction burden is not evenly distributed across the country. Certain states consistently experience higher payroll deductions than others.

* ORGANISATIONS × MEDIAN DEDUCTION RATE

1. Most organizations are clustered below 5,000 transactions.
2. A small number of organizations have very large transaction volumes (20k, 50k, 100k+).
3. The organizations with extreme deduction rates (60%, 80%, 100%) have very few transactions.
4. The high-volume organizations are mostly concentrated between 10% and 30% median deduction rate. Therefore, organization-level deduction analysis should focus on organizations with sufficient transaction volume to ensure reliable comparisons.
5. The highest organization-level deduction rates are largely driven by organizations with very small transaction counts. This means organization rankings based solely on median deduction rate are unreliable because many of the top-ranked organizations have too few payroll records to represent a stable pattern.
6. But there is no obvious relationship between organization size and deduction rate. If larger organizations systematically had higher deduction rates, it must be trending upward as transaction count increases. Instead, high-volume organizations appear spread around roughly 10% to 30% deduction rate. Deduction burden does not appear to increase with organization transaction volume. High and low deduction rates can be observed across organizations of different sizes.
7. The overall median deduction rate is 19.01%. Restricting the analysis to organizations with at least 500 transactions produces a similar median deduction rate of 19.26%. The similarity between these values suggests that excluding small organizations has little impact on the overall deduction rate distribution. The transaction threshold primarily improves the reliability of organization-level comparisons rather than changing the overall deduction burden pattern.
8. Several organizations exhibited median deduction rates of 100%. Investigation revealed that these organizations contained predominantly zero or blank net salary values despite having positive gross salary values. As a result, the calculated deduction rates reflect data availability issues or payroll anomalies rather than genuine payroll deduction patterns. So organizations with substantial proportions of zero or blank net salary records were excluded from organization-level deduction burden analysis to ensure meaningful comparisons.
9. Organization-based analysis shows that payroll deduction burden varies considerably across organizations. After restricting the analysis to organizations with at least 500 payroll transactions, the benchmark median deduction rate was 19.26%. Several organizations recorded median deduction rates above 30%, indicating that employees in these organizations experience substantially higher payroll deductions relative to their gross earnings than the typical employee in medium and large organizations. Educational organizations seems to have the highest deduction burden.

* GROSS SALARY BANDS × MEDIAN DEDUCTION RATE
1. High-income salary bands represent a very small workforce segment. Only 277 transactions fall in the 25,000–50,000 band and 104 transactions fall in the 50,000+ band.
2. Employees in the gross salary bands between 500 and 2500 per day experience the highest deduction burden. The 1000-2500 band has the highest median deduction rate (25.22%), closely followed by the 500-1000 band (24.99%). These findings are supported by more than 150,000 payroll transactions combined, making them both statistically reliable and operationally significant.
3. Employees in higher salary bands (5000-25000 per day) experience substantially lower median deduction rates, ranging from approximately 14% to 16%.
This suggests that the greatest deduction burden falls on middle-income payroll segments rather than the lowest-paid or highest-paid employees.

* DEPARTMENT × MEDIAN DEDUCTION RATE
1. A lot of departments have 100 median deduction rate and has very few transactios in most
2. Employees in several large education-related departments exhibit median deduction rates above 30% which is significantly higher than the overall department benchmark of 19.01%, including:

Dirección General de Operaciones de Servicios Educativos Iztapalapa (35.00%)

Coordinación Sectorial de Educación Preescolar (31.17%)

Departamento de Educación Primaria Tijuana (31.19%)

Dirección de Educación Primaria (30.67%)

These departments are supported by thousands of payroll transactions. This suggests that deduction burden is not distributed uniformly across departments and may be associated with differences in workforce composition, compensation structures, or payroll policies.

* JOB TITLE × MEDIAN DEDUCTION RATE
1. Teaching, educational, and administrative roles such as Técnico Superior (46.63%), Maestro de Grupo de Primaria (28.63%), Jefe de Oficina (26.14%), and Docente (23.81%) exhibit deduction rates above the workforce median of 19.01%.
2. Operational and healthcare-related roles such as Operativo (9.30%), Enfermera General 80 (4.55%), and Médico No Familiar 80 (8.81%) exhibit substantially lower deduction rates.

#### MULTIVARIATE ANALYSIS

* STATE × GROSS SALARY BAND × MEDIAN DEDUCTION RATE
1. State-level differences in deduction burden persist after controlling for gross salary bands. States such as Oaxaca and Jalisco consistently exhibit higher deduction rates than states such as Hidalgo and Sinaloa within comparable salary groups.
2. The variation in payroll deduction burden cannot be explained by salary level alone. Both geographic location (state) and salary band appear to influence deduction rates. This indicates that employees with similar earnings may experience substantially different deduction burdens depending on the state in which they are employed.

### DATA VISUALIZATION

#### Page 1: Workforce Deduction Burden Overview

* Cards provide counts of transactions, organisations, states, departments, and job titles.
* Line and clustered column chart. Line shows percentage of total transaction count. Clustered column chart shows median deduction rate of gross salary per day bands.
* Heat map shows median deduction rate of gross salary per day bands across states. Only the three largest salary bands are displayed because they represent the majority of payroll transactions (99.63%).

#### Page 2: Organisation-Level Deduction Burden Analysis

* Bar chart shows median deduction rate of organisations based on top 30 transaction count.
* Scatter plot shows median deduction rate of organisations based on transaction count. This helps distinguish organizations with consistently high deduction burdens supported by large numbers of payroll transactions from those with extreme deduction rates based on relatively few records or potential data quality issues. 

#### Page 3: Department and Job Role Deduction Analysis

* Column chart shows median deduction rate of departments based on top 30 transaction count.
* Column chart shows median deduction rate of job titles based on top 30 transaction count.

## 5. KEY INSIGHTS

#### Which workforce segments have disproportionately high deduction burdens?
Workforce segments experiencing the highest deduction burdens are primarily middle-income employees (earning between 500 and 2500 per day), employees in certain states such as Oaxaca and Jalisco, and several education-related and administrative workforce groups. Analysis across organizations, departments, and job titles consistently showed that education-related workforce segments, including teachers, professors, and educational administration functions, tend to exhibit above-average deduction rates. Further analysis of State × Gross Salary Band × Median Deduction Rate showed that state-level differences persist even within comparable salary groups. States such as Oaxaca and Jalisco consistently exhibit higher deduction rates than states such as Hidalgo and Sinaloa across similar salary bands, indicating that salary level alone does not explain the observed variation. Although substantial variation exists across organizations,  departments, and job roles, some extreme deduction rates were driven by data quality issues involving zero or missing net salary values. Overall, payroll deduction burden is unevenly distributed across the workforce and appears to be influenced by salary level, geographic location, and occupational role.

## 6. SCREENSHOTS

### Page 1: Workforce Deduction Burden Overview
![Workforce Deduction Burden Overview]()

### Page 2: Organisation-Level Deduction Burden Analysis
![Organisation-Level Deduction Burden Analysis]()

### Page 3: Department and Job Role Deduction Analysis
![Department and Job Role Deduction Analysis]()

## 7. FILES INCLUDED

* `Payroll_Deduction_Burden_Analysis_Mexico.pbix` – Power BI report
* `README.md` – Project description

## 8. How to Use

* Open `Payroll_Deduction_Burden_Analysis_Mexico.pbix` in Power BI Desktop to explore the visuals and to view the cleaned data.
