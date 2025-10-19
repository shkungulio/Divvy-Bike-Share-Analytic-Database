# Divvy Tripdata Database (2024)

## Problem Statement

The Divvy Bike Share system generates millions of trip records every year. Each record captures details such as start and end times, stations, ride type, and user category (casual vs member).
The goal of this project was to investigate how ride behaviors differ between casual and member users across:

* Time — daily, weekly, and seasonal trends
* Space — stations, routes, and geographic demand hotspots
* Behavioral metrics — ride duration, frequency, and station popularity

By building a normalized PostgreSQL database, the project aimed to transform raw monthly CSVs into a scalable analytical environment that supports fast querying, reproducible insights, and long-term data maintenance.

Key business questions included:

* How do member and casual users differ in their riding frequency and duration?
* Which stations and routes experience the highest traffic?
* What temporal patterns (daily, weekly, seasonal) drive demand fluctuations?
* How can these insights guide Divvy’s operational and marketing strategies?

## Methodology
### Data Acquisition & Preparation
* Collected 12 monthly CSV files (January–December 2024) from the Divvy tripdata portal.
* Standardized and loaded them into individual PostgreSQL staging tables.
* Implemented ETL logic in R using the RPostgres, DBI, readr, and glue packages.

### Database Design
* Created schema divvy with a star schema design:
  * Dimension tables:
    * dim_station (station info)
    * dim_bike_type (classic, docked, electric)
    * dim_member_type (member, casual)
    * dim_date (daily calendar for 2024)

  * Fact table:
    * fact_trips — central table linking all dimensions with computed fields such as ride_length_min.

* Enforced 3NF normalization, foreign key integrity, and CHECK constraints for duration validation. 

### Optimization
* Added indexes for high-performance analytical queries:
  * Time-based (started_at, started_date)
  * Segmentation (member_type_id)
  * Spatial (start_station_id, end_station_id)
  * Composite (member_type_id, started_date, route pairs)

* Introduced materialized views for pre-aggregated metrics:
  * mv_monthly_summary — monthly totals and averages by user type.

### Exploratory Data Analysis
Conducted dual-mode EDA:

* SQL-based analysis — via views (vw_daily_counts, vw_weekly_counts, vw_top_start_stations, etc.)
* R-based visualization — using ggplot2 to visualize:
  * Daily and weekly trends
  * Duration distributions
  * Top stations and routes
  
### Data Validation & Maintenance
* Verified zero missing timestamps and no duplicate ride IDs.
* Checked for positive ride durations.
* Established a monthly refresh cadence:
  * Load new data → upsert dimensions → refresh materialized views.

## Results
* Successfully built a fully normalized PostgreSQL database consolidating all 2024 Divvy trip data.
* Implemented ETL automation with helper mappings and dynamic inserts.
* Created views and dashboards for:
  * Daily and weekly ride patterns.
  * Station-level demand.
  * Ride duration distributions.
  * Member vs casual segment comparisons.

**Example Findings:**

| Metric | Observation |
|:-------|:------------|
| Total rides analyzed | ~6.5 million |
| Average ride duration (members) | ~10–12 minutes |
| Average ride duration (casual) | ~30–40 minutes |
| Peak member activity | Weekdays, morning & evening commute hours |
| Peak casual activity | Weekends & summer months |
| Top stations | Streeter Dr & Grand Ave, DuSable Lake Shore Dr & Monroe St |
| Ride duration distribution | Right-skewed; most rides under 30 minutes |

**Visual Insights:**
* Clear seasonal growth from spring to summer.
* Weekend peaks driven by casual users.
* Strong weekday commuter traffic among members.
* Prominent spatial hotspots along Chicago’s waterfront and downtown.

## Key Findings & Impact
### Behavioral Insights
Members demonstrate consistent weekday commuting behavior, while casual users exhibit strong weekend and seasonal trends — critical for staffing and fleet balancing.

### Spatial Insights
Downtown and waterfront stations dominate both starts and ends, suggesting strong tourist demand and potential for targeted promotions.

### Operational Efficiency
Indexed schema and materialized views drastically reduced query latency for time-series and spatial queries.

### Data Quality Assurance
Rigorous constraints and QA checks ensured zero duplication, consistent timestamps, and logical ride durations.

### Scalability & Sustainability
The database design supports future years’ data with minimal maintenance — simply append new monthly data and refresh summaries.

## Conclusion
This project demonstrates how careful database design, normalization, and automation can transform raw trip data into a robust analytical system.
The resulting infrastructure empowers Divvy analysts to:

* Efficiently query millions of records
* Identify behavioral trends by user segment
* Optimize station operations and marketing focus
* Extend analysis toward predictive modeling and demand forecasting
