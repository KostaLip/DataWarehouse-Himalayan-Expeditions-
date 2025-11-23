Himalayan Expeditions Data Warehouse
A comprehensive data warehousing solution for analyzing historical Himalayan mountaineering expeditions (1900-2024). Built as an academic project demonstrating complete ETL pipeline implementation, dimensional modeling, and business intelligence reporting.

-- Project Overview
This project transforms raw CSV data from the Himalayan Expeditions dataset (Kaggle) into a structured data warehouse with a star schema design, enabling efficient analytical queries and business intelligence reporting.
Key Achievement: Converted fragmented expedition data spanning 124 years into a centralized analytical system with 8 dimension tables and 1 fact table.

-- Technologies Used

Microsoft SQL Server - Database management system
SSIS (SQL Server Integration Services) - ETL processes and data integration
SSRS (SQL Server Reporting Services) - Report development and visualization
MsSQL - Complex analytical queries
Visual Studio / SSDT - Development environment

-- Data Source
Dataset: Himalayan Expeditions from Kaggle

Time Period: 1900-2024 (124 years)
Source Files:

exped.csv - Expedition details
members.csv - Individual member participation
peaks.csv - Mountain peak information
refer.csv - References and articles


-- Data Warehouse Architecture
Star Schema Design
Fact Table:

FactExpeditionMember - Granularity: One member's participation in one expedition

Measures: personal_highpoint, age_at_expedition
Flags: success, is_oxygen_used, is_leader, is_death, expedition_highpoint_reached

Dimension Tables:

DimDate - Time dimension (full date, day, month, quarter, year)
DimNation - Member nationalities (including dual citizenship)
DimRoute - Climbing routes used
DimStatus - Member roles (generalized from detailed subcategories)
DimTerminationReason - Reasons for expedition termination
DimPeak - Mountain peaks (name, location, height, region, accessibility)
DimExpedition - Expedition details (host country, season, success rate, highpoint)
DimMember - Unique climbers (name, gender, birth year, occupation, Sherpa status)

Design Decisions

Granularity: Individual member participation per expedition
Schema Type: Star schema for optimized analytical queries
Key Features:

Surrogate keys for all dimensions
Unknown value handling (-1 for numeric, "Unknown" for text)
Slowly Changing Dimensions support
Eliminated duplicates through careful transformation



-- ETL Process (SSIS)
Extraction

Read data from multiple CSV source files
Handle large volumes of historical data

Transformation

Data Cleaning: Remove duplicates, handle NULL/empty values
Data Conversion: Convert to appropriate data types (Unicode WSTR)
Generalization: Consolidated detailed status roles into broader categories
Derived Columns: Calculate age, remove unnecessary characters
Lookup Transformations: Map natural keys to surrogate keys
Aggregation: Extract unique members from participation records

Loading

Conditional drops and creates: Database schema management
Dimension loading: Sequential population with unknown value handling
Fact table loading:

Merge Join on expedition ID
Multiple Lookup components for foreign key resolution
Union All for combining matched and unmatched records


Key ETL Challenges Solved

Member Uniqueness: Same member can appear multiple times with different expedition IDs - solved by aggregating on all personal attributes
Dual Citizenship: Treated as separate nationality entry
Missing Data: Standardized handling with -1 (numeric) and "Unknown" (text)
Status Generalization: Reduced 50+ status variations to ~10 meaningful categories

-- SSRS Reports (7 Business Intelligence Reports)

Success Rate by Nationality - Which nations have the most successful climbers
Common Termination Reasons - Why do members abandon expeditions
Most Dangerous Peaks - Peaks with highest fatality rates
Success Rate by Age Group - Performance across different age ranges
Sherpas vs Other Nationalities - Comparative success analysis
Impact of Supplemental Oxygen - Correlation between O2 use and success
Expedition Popularity by Decade - Historical trends in expedition frequency

-- Key Learning Points

ETL Pipeline Design: Built complete data integration workflow handling multiple sources
Dimensional Modeling: Designed star schema with proper granularity
Data Quality: Implemented comprehensive data cleaning and standardization
Lookup Transformations: Mastered surrogate key resolution with multiple conditions
Business Intelligence: Created actionable reports from raw data
Problem Solving: Handled complex data relationships and member uniqueness challenges

-- Getting Started
Prerequisites

Microsoft SQL Server 2016+
SQL Server Data Tools (SSDT)
SQL Server Integration Services (SSIS)
SQL Server Reporting Services (SSRS)

Setup

Download Dataset

Get Himalayan Expeditions dataset from Kaggle
Place CSV files in designated data folder


Run ETL Package

Deploy Reports

-- Key Statistics

Time Span: 124 years of expedition data (1900-2024)
Dimensions: 8 dimension tables
Fact Granularity: Individual member participation per expedition
Reports: 7 comprehensive analytical reports
Data Quality: Handled missing values, duplicates, and inconsistencies
