# Himalayan Expeditions Data Warehouse

A comprehensive data warehousing solution for analyzing historical Himalayan mountaineering expeditions (1900-2024). Built as an academic project demonstrating complete ETL pipeline implementation, dimensional modeling, and business intelligence reporting.

## Project Overview

This project transforms raw CSV data from the Himalayan Expeditions dataset (Kaggle) into a structured data warehouse with a star schema design, enabling efficient analytical queries and business intelligence reporting.

**Key Achievement:** Converted fragmented expedition data spanning 124 years into a centralized analytical system with 8 dimension tables and 1 fact table.

## Technologies Used

- **Microsoft SQL Server** - Database management system
- **SSIS (SQL Server Integration Services)** - ETL processes and data integration
- **SSRS (SQL Server Reporting Services)** - Report development and visualization
- **T-SQL** - Complex analytical queries
- **Visual Studio / SSDT** - Development environment

## Data Source

**Dataset:** Himalayan Expeditions from Kaggle
- **Time Period:** 1900-2024 (124 years)
- **Source Files:** 
  - `exped.csv` - Expedition details
  - `members.csv` - Individual member participation
  - `peaks.csv` - Mountain peak information
  - `refer.csv` - References and articles

## Data Warehouse Architecture

### Star Schema Design

**Fact Table:**
- **FactExpeditionMember** - Granularity: One member's participation in one expedition
  - Measures: personal_highpoint, age_at_expedition
  - Flags: success, is_oxygen_used, is_leader, is_death, expedition_highpoint_reached

**Dimension Tables:**
1. **DimDate** - Time dimension (full date, day, month, quarter, year)
2. **DimNation** - Member nationalities (including dual citizenship)
3. **DimRoute** - Climbing routes used
4. **DimStatus** - Member roles (generalized from detailed subcategories)
5. **DimTerminationReason** - Reasons for expedition termination
6. **DimPeak** - Mountain peaks (name, location, height, region, accessibility)
7. **DimExpedition** - Expedition details (host country, season, success rate, highpoint)
8. **DimMember** - Unique climbers (name, gender, birth year, occupation, Sherpa status)

### Design Decisions

- **Granularity:** Individual member participation per expedition
- **Schema Type:** Star schema for optimized analytical queries
- **Key Features:**
  - Surrogate keys for all dimensions
  - Unknown value handling (-1 for numeric, "Unknown" for text)
  - Slowly Changing Dimensions support
  - Eliminated duplicates through careful transformation

## ETL Process (SSIS)

### Extraction
- Read data from multiple CSV source files
- Handle large volumes of historical data

### Transformation
- **Data Cleaning:** Remove duplicates, handle NULL/empty values
- **Data Conversion:** Convert to appropriate data types (Unicode WSTR)
- **Generalization:** Consolidated detailed status roles into broader categories
- **Derived Columns:** Calculate age, remove unnecessary characters
- **Lookup Transformations:** Map natural keys to surrogate keys
- **Aggregation:** Extract unique members from participation records

### Loading
- **Conditional drops and creates:** Database schema management
- **Dimension loading:** Sequential population with unknown value handling
- **Fact table loading:** 
  - Merge Join on expedition ID
  - Multiple Lookup components for foreign key resolution
  - Union All for combining matched and unmatched records

### Key ETL Challenges Solved

1. **Member Uniqueness:** Same member can appear multiple times with different expedition IDs - solved by aggregating on all personal attributes
2. **Dual Citizenship:** Treated as separate nationality entry
3. **Missing Data:** Standardized handling with -1 (numeric) and "Unknown" (text)
4. **Status Generalization:** Reduced 50+ status variations to ~10 meaningful categories

## SSRS Reports (7 Business Intelligence Reports)

1. **Success Rate by Nationality** - Which nations have the most successful climbers
2. **Common Termination Reasons** - Why do members abandon expeditions
3. **Most Dangerous Peaks** - Peaks with highest fatality rates
4. **Success Rate by Age Group** - Performance across different age ranges
5. **Sherpas vs Other Nationalities** - Comparative success analysis
6. **Impact of Supplemental Oxygen** - Correlation between O2 use and success
7. **Expedition Popularity by Decade** - Historical trends in expedition frequency

## Sample Analytical Queries

```sql
-- Success rate by nationality
SELECT 
    n.nation_name,
    COUNT(*) AS total_expeditions,
    SUM(CAST(f.success AS INT)) AS successful_climbs,
    CAST(SUM(CAST(f.success AS INT)) * 100.0 / COUNT(*) AS DECIMAL(5,2)) AS success_rate
FROM FactExpeditionMember f
JOIN DimNation n ON f.nation_id = n.nation_id
WHERE n.nation_id > 0
GROUP BY n.nation_name
ORDER BY success_rate DESC;

-- Most dangerous peaks
SELECT TOP 10
    p.peak_name,
    p.height_meters,
    COUNT(*) AS total_deaths
FROM FactExpeditionMember f
JOIN DimPeak p ON f.peak_id = p.peak_id
WHERE f.is_death = 1
GROUP BY p.peak_name, p.height_meters
ORDER BY total_deaths DESC;

-- Impact of oxygen on success
SELECT 
    CASE WHEN is_oxygen_used = 1 THEN 'With O2' ELSE 'Without O2' END AS oxygen_usage,
    COUNT(*) AS total_attempts,
    SUM(CAST(success AS INT)) AS successful_summits,
    CAST(SUM(CAST(success AS INT)) * 100.0 / COUNT(*) AS DECIMAL(5,2)) AS success_rate
FROM FactExpeditionMember
GROUP BY is_oxygen_used;
```

## Key Learning Points

- **ETL Pipeline Design:** Built complete data integration workflow handling multiple sources
- **Dimensional Modeling:** Designed star schema with proper granularity
- **Data Quality:** Implemented comprehensive data cleaning and standardization
- **Lookup Transformations:** Mastered surrogate key resolution with multiple conditions
- **Business Intelligence:** Created actionable reports from raw data
- **Problem Solving:** Handled complex data relationships and member uniqueness challenges

## Getting Started

### Prerequisites
- Microsoft SQL Server 2016+
- SQL Server Data Tools (SSDT)
- SQL Server Integration Services (SSIS)
- SQL Server Reporting Services (SSRS)

### Setup

1. **Download Dataset**
   - Get Himalayan Expeditions dataset from Kaggle
   - Place CSV files in designated data folder

2. **Run ETL Package**

3. **Deploy Reports**

## Key Statistics

- **Time Span:** 124 years of expedition data (1900-2024)
- **Dimensions:** 8 dimension tables
- **Fact Granularity:** Individual member participation per expedition
- **Reports:** 7 comprehensive analytical reports
- **Data Quality:** Handled missing values, duplicates, and inconsistencies

## Future Enhancements

- **Cloud Migration:** Move to Azure SQL Database for scalability
- **AI/ML Integration:** 
  - Predict expedition success rates
  - Recommend optimal peaks based on climber profile
  - Suggest best seasons and routes
- **Real-time Updates:** Integrate with live expedition tracking
- **Interactive Dashboards:** Power BI integration for dynamic analysis
- **Advanced Analytics:** Weather correlation analysis, equipment impact studies
