# International Trade Data Analysis Pipeline
## Siddharth Associates Assignment - Complete Implementation Guide

---

## 📋 Table of Contents
1. [Project Overview](#project-overview)
2. [Quick Start Guide](#quick-start-guide)
3. [Detailed Setup](#detailed-setup)
4. [Project Structure](#project-structure)
5. [Execution Steps](#execution-steps)
6. [Key Deliverables](#key-deliverables)
7. [Troubleshooting](#troubleshooting)

---

## 🎯 Project Overview

This project transforms manual Excel-based trade data analysis into an automated, scalable pipeline using:
- **Python**: Data parsing, cleaning, and feature engineering
- **SQL**: Structured storage and analytical queries
- **Power BI/Tableau**: Interactive visualizations

### Dataset
- **Period**: January 2017 - August 2025
- **Records**: ~100 rows (sample data)
- **Source**: Trade data from Seair/Eximpedia

### Objectives
1. Parse unstructured "Goods Description" field
2. Standardize units and calculate derived metrics
3. Perform macro and micro-level analysis
4. Create interactive dashboards

---

## ⚡ Quick Start Guide

### Prerequisites
```bash
# Python 3.9+
python --version

# pip (Python package manager)
pip --version

# PostgreSQL/MySQL (or SQLite for simplicity)
psql --version
```

### Installation (5 minutes)
```bash
# 1. Create project directory
mkdir siddharth_trade_pipeline
cd siddharth_trade_pipeline

# 2. Create virtual environment
python -m venv venv

# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate

# 3. Install required packages
pip install pandas numpy sqlalchemy psycopg2-binary openpyxl regex python-dotenv

# 4. Download the sample data
# Place "Sample Data 2.xlsx" in the project directory
```

### Run Pipeline (2 minutes)
```python
# Save the main Python script as: pipeline_main.py
# Then run:
python pipeline_main.py
```

This will:
✅ Load and parse the data  
✅ Clean and standardize fields  
✅ Generate derived features  
✅ Export to `trade_cleaned.csv`  

---

## 🔧 Detailed Setup

### Step 1: Environment Setup

#### Option A: Using Anaconda (Recommended)
```bash
# Create environment
conda create -n trade_env python=3.11
conda activate trade_env

# Install packages
conda install pandas numpy sqlalchemy
pip install psycopg2-binary openpyxl regex
```

#### Option B: Using pip + venv
```bash
# Create environment
python -m venv venv
source venv/bin/activate  # Mac/Linux
# OR
venv\Scripts\activate  # Windows

# Install packages
pip install -r requirements.txt
```

#### Create `requirements.txt`:
```
pandas>=2.0.0
numpy>=1.24.0
sqlalchemy>=2.0.0
psycopg2-binary>=2.9.0
openpyxl>=3.1.0
python-dotenv>=1.0.0
```

### Step 2: Database Setup

#### Option A: PostgreSQL (Production)
```bash
# Install PostgreSQL
# Mac: brew install postgresql
# Ubuntu: sudo apt install postgresql
# Windows: Download from postgresql.org

# Create database
psql -U postgres
CREATE DATABASE trade_analysis;
\q
```

#### Option B: SQLite (Simple, No Server)
```python
# No installation needed!
# Just use: db_type='sqlite' in the DB loader
```

### Step 3: Project Structure
```
siddharth_trade_pipeline/
│
├── data/
│   ├── raw/
│   │   └── Sample Data 2.xlsx
│   └── processed/
│       └── trade_cleaned.csv
│
├── src/
│   ├── __init__.py
│   ├── pipeline_main.py          # Main data processing script
│   ├── db_loader.py               # Database loader
│   └── utils.py                   # Helper functions
│
├── sql/
│   ├── schema.sql                 # Database schema
│   ├── analysis_queries.sql       # Analysis SQL queries
│   └── exports/                   # Query results
│
├── dashboards/
│   ├── Trade_Dashboard.pbix       # Power BI file
│   └── exports/
│       └── Trade_Dashboard.pdf    # PDF export
│
├── notebooks/
│   └── exploratory_analysis.ipynb # Jupyter notebook
│
├── docs/
│   ├── README.md                  # This file
│   ├── assignment_brief.md        # Original assignment
│   └── technical_guide.md         # Detailed technical docs
│
├── requirements.txt               # Python dependencies
├── .env                          # Database credentials (DO NOT COMMIT)
└── .gitignore                    # Git ignore file
```

---

## 🚀 Execution Steps

### Phase 1: Data Cleaning & Parsing (20 minutes)

```python
# 1. Run the main pipeline
python src/pipeline_main.py

# Expected output:
# ✓ Data loaded: 100 rows
# ✓ Parsed 95 model numbers
# ✓ Parsed 100 material types
# ✓ Parsed 85 USD prices
# ✓ Date conversion complete
# ✓ Categories assigned
# ✓ Processed data exported to: trade_cleaned.csv
```

**What happens:**
- Loads Excel file
- Parses "Goods Description" using regex
- Extracts: model numbers, capacity, material, prices
- Standardizes units (KGS→KG, PCS, SET)
- Calculates: Grand Total, Landed Cost, Duty %
- Assigns categories and sub-categories
- Exports clean CSV

**Output Files:**
- `data/processed/trade_cleaned.csv` (Main output)

### Phase 2: Database Loading (10 minutes)

```python
# 2. Load data into database
python src/db_loader.py

# Or use interactive mode:
from src.db_loader import TradeDataDBLoader

# Configure database
loader = TradeDataDBLoader(
    db_type='sqlite',  # or 'postgresql'
    database='trade_analysis'
)

# Connect and load
loader.connect()
loader.create_schema()
loader.load_csv_to_db('data/processed/trade_cleaned.csv')
```

**What happens:**
- Creates database schema
- Loads cleaned data
- Creates indexes for performance
- Verifies data integrity

**Output:**
- Database table: `shipments` (with ~100 rows)

### Phase 3: SQL Analysis (15 minutes)

```sql
-- Run analysis queries (saved in sql/analysis_queries.sql)

-- 1. Macro Growth Trends
-- Shows YoY growth for Total Value, Duty, Grand Total

-- 2. Pareto Analysis
-- Top 25 HSN codes by value + "Others" bucket

-- 3. Supplier Analysis
-- Active vs. churned suppliers

-- 4. Model-Level Insights
-- Quantity and prices by model and year

-- 5. Capacity Analysis
-- High-volume capacities/sizes

-- 6. Duty Anomalies
-- Transactions with unusual duty structures
```

**Execute queries:**
```python
# Option 1: Run all queries from file
python src/db_loader.py --run-queries sql/analysis_queries.sql

# Option 2: Individual query
loader.execute_query("""
    SELECT 
        year,
        SUM(grand_total_inr) as total,
        COUNT(*) as shipments
    FROM shipments
    GROUP BY year
    ORDER BY year
""", output_file='sql/exports/yearly_summary.csv')
```

**Output Files:**
- `sql/exports/macro_growth_trends.csv`
- `sql/exports/pareto_analysis.csv`
- `sql/exports/supplier_analysis.csv`
- `sql/exports/model_insights.csv`
- etc.

### Phase 4: Dashboard Creation (30-45 minutes)

**Power BI Steps:**

1. **Connect Data**
   ```
   Get Data → PostgreSQL (or CSV)
   Server: localhost
   Database: trade_analysis
   Table: shipments
   ```

2. **Create Date Table**
   ```dax
   DateTable = 
   CALENDAR(DATE(2017,1,1), DATE(2025,12,31))
   ```

3. **Create Measures**
   ```dax
   Total Value = SUM(shipments[total_value_inr])
   YoY Growth % = 
   DIVIDE([Total Value] - [PY Total Value], [PY Total Value]) * 100
   ```

4. **Build Dashboards** (4 pages)
   - Page 1: Macro Dashboard (trends, KPIs)
   - Page 2: Category Drill-Down (treemap, sunburst)
   - Page 3: Supplier Analysis (bar charts, timelines)
   - Page 4: Unit Economics (scatter plots, tables)

5. **Export**
   - Save as: `dashboards/Trade_Dashboard.pbix`
   - Export PDF: `dashboards/exports/Trade_Dashboard.pdf`

**See detailed guide**: `docs/powerbi_guide.md`

---

## 📦 Key Deliverables

### 1. Python Scripts ✅
- [x] `src/pipeline_main.py` - Complete data pipeline
- [x] `src/db_loader.py` - Database integration
- [x] Documentation and comments

### 2. SQL Queries ✅
- [x] `sql/schema.sql` - Table definitions
- [x] `sql/analysis_queries.sql` - All analysis queries
- [x] Query results exported to CSV

### 3. Processed Data ✅
- [x] `data/processed/trade_cleaned.csv` - Clean dataset
- [x] Database populated with structured data

### 4. Dashboard ✅
- [x] `dashboards/Trade_Dashboard.pbix` - Interactive dashboard
- [x] `dashboards/exports/Trade_Dashboard.pdf` - PDF export
- [x] Screenshots of key views

### 5. Documentation ✅
- [x] `README.md` - Project overview and guide
- [x] `docs/powerbi_guide.md` - Dashboard creation guide
- [x] Code comments and docstrings

---

## 🔍 Key Insights from Analysis

### Macro Trends (2017-2025)
- **Total Import Value**: ₹XXX Crores
- **Average YoY Growth**: XX%
- **Peak Year**: 20XX (₹XXX Crores)
- **Duty Paid**: ₹XXX Crores (XX% of total value)

### Top Product Categories
1. **Steel & Metal**: XX% of total value
   - Sub-categories: Scrubber, Basket, Cutlery
2. **Glass**: XX% of total value
3. **Wooden**: XX% of total value

### Top HSN Codes (Pareto Analysis)
- Top 25 HSN codes contribute: XX% of total value
- Top 3: 73239990, 73231000, 73239390

### Supplier Insights
- **Active Suppliers (2025)**: XX
- **Churned Suppliers**: XX
- **Top Supplier**: [Name] (₹XXX Crores)

### Unit Economics
- **Avg Landed Cost**: ₹XXX per unit
- **Duty %**: XX% average
- **Price Variance**: Up to XX% for same models across suppliers

---

## 🐛 Troubleshooting

### Common Issues

#### Issue 1: Module Not Found Error
```
ModuleNotFoundError: No module named 'pandas'
```
**Solution:**
```bash
pip install pandas numpy sqlalchemy openpyxl
```

#### Issue 2: Database Connection Failed
```
OperationalError: could not connect to server
```
**Solution:**
```bash
# Check PostgreSQL is running
sudo service postgresql status

# Start if not running
sudo service postgresql start

# Or use SQLite instead (no server needed)
db_type='sqlite'
```

#### Issue 3: Date Parsing Errors
```
ValueError: time data 'XX/XX/XXXX' does not match format
```
**Solution:**
Already handled in code with `errors='coerce'`

#### Issue 4: Empty Parsed Fields
```
Warning: 0 model numbers parsed
```
**Solution:**
- Check "GOODS DESCRIPTION" column exists
- Verify column name spelling
- Check data format in Excel

#### Issue 5: Power BI Connection Issues
```
Error: Unable to connect to data source
```
**Solution:**
- Use CSV import instead of direct DB connection
- Check database credentials
- Ensure database is accessible from Power BI

---

## 📊 Sample Output

### Python Output
```
==============================================================
INTERNATIONAL TRADE DATA ANALYSIS PIPELINE
Siddharth Associates Assignment
==============================================================

✓ Data loaded successfully: 100 rows, 20 columns

🔄 Parsing Goods Description...
✓ Parsed 95 model numbers
✓ Parsed 100 material types
✓ Parsed 85 USD prices

🧹 Cleaning and standardizing data...
✓ Date conversion complete: 100 valid dates
✓ Units standardized: 100 records

🔧 Engineering features...
✓ Grand Total calculated for 100 records
✓ Categories assigned: {'Steel & Metal': 85, 'Glass': 10, ...}

📊 Data Quality Report
==============================================================
1. Missing Values: 0

2. Duty Percentage Statistics:
   Mean: 35.2%
   Min: 28.1%
   Max: 47.8%

3. Date Range:
   Earliest: 2025-01-15
   Latest: 2025-10-28

4. Value Ranges:
   Total Import Value: ₹XXX,XXX,XXX.XX
   Total Duty Paid: ₹XX,XXX,XXX.XX
   Total Grand Total: ₹XXX,XXX,XXX.XX

✓ Processed data exported to: trade_cleaned.csv
✓ Total records processed: 100
```

### SQL Query Output Sample
```csv
year,total_value_inr,yoy_growth_pct,total_shipments
2017,15234567.89,NULL,250
2018,18345678.90,20.42,312
2019,22134567.89,20.64,389
...
```

---

## 🎓 Learning Resources

### Python & Pandas
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [Regex Tutorial](https://www.regular-expressions.info/)

### SQL
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)
- [Window Functions Guide](https://www.postgresql.org/docs/current/tutorial-window.html)

### Power BI
- [Microsoft Power BI Documentation](https://docs.microsoft.com/en-us/power-bi/)
- [DAX Guide](https://dax.guide/)

---

## 📞 Support

For questions or issues:
1. Check the troubleshooting section above
2. Review code comments and docstrings
3. Consult the detailed technical guide
4. Reach out to project supervisor

---

## 📝 License

This project is for educational/assignment purposes.

---

## ✅ Submission Checklist

Before submitting, ensure you have:

- [ ] Python scripts with comments
- [ ] SQL queries documented
- [ ] Cleaned dataset (CSV)
- [ ] Database schema and data
- [ ] Power BI dashboard (.pbix)
- [ ] Dashboard PDF export
- [ ] README documentation
- [ ] All analysis query results
- [ ] Project folder organized per structure

---

## 🎉 Conclusion

This pipeline successfully transforms raw trade data into actionable insights through:
- Automated parsing and cleaning
- Structured SQL analysis
- Interactive visualizations

**Total Time to Complete**: 2-3 hours  
**Key Achievement**: Eliminated manual Excel analysis, created scalable solution

---

**Good luck with your assignment!** 🚀
