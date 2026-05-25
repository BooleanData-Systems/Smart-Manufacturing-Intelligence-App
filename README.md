# Smart Manufacturing Intelligence

Production-grade OEE monitoring, predictive maintenance, and AI-powered action planning — entirely within your Snowflake account.

## Overview

This Native App provides a unified real-time view of manufacturing performance with a medallion architecture (Bronze > Silver > Gold). It calculates OEE, tracks machine health, predicts failures, and generates prioritized action plans automatically.

- **OEE & Production** — Overall Equipment Effectiveness scoring, machine-wise breakdown, shift comparison
- **Downtime & Utilization** — Downtime by reason/machine, asset utilization tracking, time state distribution
- **Alerts & Maintenance** — Temperature/vibration monitoring, severity classification, maintenance recommendations
- **Predictive Maintenance** — Failure probability scoring, risk levels, sensor threshold analysis
- **Action Plan** — Prioritized recommendations based on live sensor data, OEE trends, and downtime patterns

## Configuration Steps

1. **Install the application** from the Snowflake Marketplace.
2. **Grant permissions** — Navigate to the app's Permissions tab and bind your 2 Bronze table references.
3. **Pipeline runs automatically** — On first launch, `BUILD_PIPELINE()` transforms Bronze data into Silver and Gold layers.
4. **Explore** — The dashboard is fully operational once the pipeline completes.

## Required Input References (Consumer-Provided)

| Reference | Description | Required Columns |
|-----------|-------------|-----------------|
| BRONZE_MACHINE_EVENTS | Raw machine events from SCADA/PLC/IoT | `RAW_EVENT_ID` (VARCHAR), `RAW_TIMESTAMP` (VARCHAR), `RAW_PLANT_ID` (VARCHAR), `RAW_LINE_ID` (VARCHAR), `RAW_MACHINE_ID` (VARCHAR), `RAW_EVENT_TYPE` (VARCHAR), `RAW_STATUS` (VARCHAR), `RAW_DURATION_MIN` (VARCHAR), `RAW_REASON` (VARCHAR), `RAW_OPERATOR_ID` (VARCHAR) |
| BRONZE_PRODUCTION_DATA | Raw production records from MES/ERP | `RAW_RECORD_ID` (VARCHAR), `RAW_TIMESTAMP` (VARCHAR), `RAW_PLANT_ID` (VARCHAR), `RAW_LINE_ID` (VARCHAR), `RAW_MACHINE_ID` (VARCHAR), `RAW_PRODUCT_ID` (VARCHAR), `RAW_UNITS_PRODUCED` (VARCHAR), `RAW_UNITS_DEFECTIVE` (VARCHAR), `RAW_CYCLE_TIME_SEC` (VARCHAR), `RAW_IDEAL_CYCLE_SEC` (VARCHAR), `RAW_TEMPERATURE_C` (VARCHAR), `RAW_VIBRATION_MM` (VARCHAR) |

## How the Pipeline Works

After binding the 2 Bronze references, the app generates all downstream tables internally:

**Silver Layer:**
| Table | Description |
|-------|-------------|
| silver.machine_events | Cleansed, typed machine events with shift assignment |
| silver.production_data | Validated production records with calculated good units |

**Gold Layer:**
| Table | Description |
|-------|-------------|
| gold.dim_plant | Plant dimension |
| gold.dim_line | Production line dimension |
| gold.dim_machine | Machine dimension |
| gold.fact_oee | OEE fact with availability, performance, quality per machine/shift/day |
| gold.fact_utilization | Asset utilization (running/downtime/idle split) |
| gold.fact_alerts | Predictive maintenance alerts with severity and recommendations |
| gold.vw_machine_performance | Semantic view joining OEE facts with dimensions |
| gold.vw_asset_utilization | Utilization semantic view |
| gold.vw_work_orders | Generated work order tracking |
| gold.transform_log | Pipeline execution audit log |

## KPIs Calculated Automatically

- OEE % (Availability × Performance × Quality)
- Availability, Performance, Quality individually
- Downtime minutes by reason and machine
- Asset utilization percentage
- Failure probability scoring (temperature + vibration + OEE degradation)
- Reject/yield rate

## Permissions

The app requests **SELECT** access on 2 Bronze tables only. All Silver and Gold tables are created internally. No data leaves your account.

## Industries Supported

Electronics Manufacturing, Automotive, Pharmaceutical, Food & Beverage, Aerospace & Defense, Chemical Processing, Consumer Goods.

## Version History

| Version | Date | Notes |
|---------|------|-------|
| 1.0.0 | 2026-05-25 | Initial marketplace release |
