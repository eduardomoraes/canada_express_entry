# Data Dictionary: Canada Express Entry Analysis

## Overview

This document provides detailed definitions and specifications for all data fields used in the Canada Express Entry immigration draws analysis. The data structure follows the official IRCC reporting format and includes derived analytical fields.

## Primary Data Tables

### 1. Express Entry Draws (Main Fact Table)

| Field Name | Data Type | Description | Example | Notes |
|------------|-----------|-------------|---------|--------|
| `draw_date` | Date | Date when the Express Entry draw took place | 2024-01-15 | Format: YYYY-MM-DD |
| `draw_number` | Integer | Sequential draw number assigned by IRCC | 275 | Unique identifier for each draw |
| `program_type` | Text | Type of Express Entry program | "General", "CEC", "FSW", "PNP" | See Program Types section |
| `minimum_crs_score` | Integer | Minimum CRS score required for invitation | 542 | Range: 75-1200 |
| `invitations_issued` | Integer | Number of invitations to apply (ITAs) issued | 1500 | Positive integer |
| `tie_breaking_rule` | Text | Rule applied when multiple candidates have the same CRS score | "2023-12-15 14:30:00 UTC" | Date and time format |
| `draw_size_category` | Text | Categorization based on invitation volume | "Large", "Medium", "Small" | Derived field |

### 2. Date Dimension Table

| Field Name | Data Type | Description | Example | Notes |
|------------|-----------|-------------|---------|--------|
| `date_key` | Date | Primary date key | 2024-01-15 | Links to draw_date |
| `year` | Integer | Year component | 2024 | Extracted from date |
| `quarter` | Integer | Quarter number (1-4) | 1 | Q1: Jan-Mar, Q2: Apr-Jun, etc. |
| `month` | Integer | Month number (1-12) | 1 | January = 1, December = 12 |
| `month_name` | Text | Full month name | "January" | For display purposes |
| `week_number` | Integer | Week number of the year (1-53) | 3 | ISO week numbering |
| `day_of_week` | Integer | Day of week (1-7) | 1 | Monday = 1, Sunday = 7 |
| `day_name` | Text | Full day name | "Monday" | For display purposes |
| `is_weekend` | Boolean | Whether the date falls on weekend | False | True for Saturday/Sunday |
| `fiscal_year` | Text | Canadian government fiscal year | "FY2024" | April 1 - March 31 |

### 3. Program Dimension Table

| Field Name | Data Type | Description | Example | Notes |
|------------|-----------|-------------|---------|--------|
| `program_code` | Text | Short program identifier | "CEC" | Primary key |
| `program_name` | Text | Full program name | "Canadian Experience Class" | Official program name |
| `program_category` | Text | Program category | "Federal Economic" | Grouping classification |
| `eligibility_criteria` | Text | Brief eligibility description | "Canadian work experience required" | Summary of requirements |
| `launch_date` | Date | When program became available | 2015-01-01 | Express Entry launch |

## Calculated Fields and Measures

### Statistical Measures

| Measure Name | Formula Type | Description | Calculation |
|--------------|--------------|-------------|-------------|
| `avg_crs_score` | DAX | Average CRS score across selected period | AVERAGE(draws[minimum_crs_score]) |
| `median_crs_score` | DAX | Median CRS score | MEDIAN(draws[minimum_crs_score]) |
| `crs_score_variance` | DAX | Variance in CRS scores | VAR.P(draws[minimum_crs_score]) |
| `total_invitations` | DAX | Sum of all invitations issued | SUM(draws[invitations_issued]) |
| `draw_frequency` | DAX | Average days between draws | Custom calculation |
| `score_trend` | DAX | Moving average trend indicator | Custom moving average |

### Time-Based Calculations

| Field Name | Calculation | Description | Example |
|------------|-------------|-------------|---------|
| `days_since_previous_draw` | Calculated Column | Days elapsed since previous draw | 14 |
| `draws_per_month` | Measure | Number of draws in the month | 2 |
| `monthly_invitations` | Measure | Total invitations per month | 6000 |
| `year_over_year_change` | Measure | YoY change in average CRS score | +5.2% |
| `rolling_30_day_avg` | Measure | 30-day rolling average of CRS scores | 485.7 |

## Program Type Classifications

### Primary Programs

| Code | Full Name | Description |
|------|-----------|-------------|
| `General` | General Draw | Open to all Express Entry programs |
| `CEC` | Canadian Experience Class | For candidates with Canadian work experience |
| `FSW` | Federal Skilled Worker | For skilled workers with foreign work experience |
| `FST` | Federal Skilled Trades | For skilled tradespersons |
| `PNP` | Provincial Nominee Program | For provincial nominees |

### Specialized Draws

| Code | Full Name | Description |
|------|-----------|-------------|
| `French` | French Language Proficiency | For French-speaking candidates |
| `Healthcare` | Healthcare Workers | For healthcare professionals |
| `STEM` | Science, Technology, Engineering, Math | For STEM occupations |
| `Transport` | Transport Occupations | For transportation workers |
| `Agriculture` | Agriculture and Agri-food | For agricultural workers |

## Data Quality Indicators

### Completeness Flags

| Field Name | Data Type | Description | Values |
|------------|-----------|-------------|---------|
| `data_complete` | Boolean | All required fields populated | True/False |
| `score_validated` | Boolean | CRS score within valid range | True/False |
| `official_source` | Boolean | Confirmed from official IRCC source | True/False |
| `data_confidence` | Text | Confidence level in data accuracy | High/Medium/Low |

### Data Source Information

| Field Name | Data Type | Description | Example |
|------------|-----------|-------------|---------|
| `source_url` | Text | URL of original data source | "https://www.canada.ca/..." |
| `extraction_date` | DateTime | When data was collected | 2024-01-16 09:30:00 |
| `last_verified` | Date | Last verification date | 2024-01-15 |
| `data_version` | Text | Version identifier | "v2.1" |

## Value Ranges and Constraints

### CRS Score Ranges

| Category | Minimum | Maximum | Description |
|----------|---------|---------|-------------|
| Theoretical Minimum | 0 | 300 | Without job offer or nomination |
| Practical Minimum | 300 | 400 | Rarely invited at this range |
| Common Range | 400 | 600 | Most invitations fall here |
| High Score | 600 | 1000 | Competitive scores |
| Maximum Possible | 1000 | 1200 | With nomination and job offer |

### Invitation Volume Categories

| Category | Range | Description |
|----------|-------|-------------|
| Small | 1-1000 | Limited, targeted draws |
| Medium | 1001-3000 | Regular draw size |
| Large | 3001-5000 | High-volume draws |
| Extra Large | 5000+ | Special or catch-up draws |

## Data Update Schedule

### Regular Updates
- **Weekly**: New draw data when available
- **Monthly**: Data validation and cleanup
- **Quarterly**: Comprehensive review and reconciliation

### Historical Data
- **Coverage**: January 1, 2015 to present
- **Completeness**: 99.5% of official draws captured
- **Validation**: Cross-referenced with multiple sources

## Data Lineage

### Source Systems
1. **Primary**: IRCC official website and press releases
2. **Secondary**: Government of Canada Open Data Portal
3. **Validation**: Immigration news websites and legal sources

### Processing Steps
1. Data extraction from official sources
2. Data validation and cleansing
3. Standardization and formatting
4. Quality assurance checks
5. Loading into Power BI data model

## Notes and Conventions

### Date Handling
- All dates stored in UTC
- Canadian Eastern Time used for tie-breaking rules
- Fiscal year follows Canadian government calendar (April 1 - March 31)

### Missing Data
- Missing CRS scores indicated as NULL
- Missing invitation counts estimated based on program patterns
- Data gaps documented in quality indicators

### Calculation Precision
- CRS scores: Integer values only
- Percentages: Rounded to 1 decimal place
- Averages: Rounded to nearest whole number

## Change Log

| Date | Version | Changes |
|------|---------|---------|
| 2024-01-15 | 1.0 | Initial data dictionary |
| Future | 2.0 | Planned updates based on IRCC changes |

---

*This data dictionary is maintained alongside the analysis and updated whenever the data structure or definitions change.*