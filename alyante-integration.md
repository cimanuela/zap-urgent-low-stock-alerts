# TeamSystem Alyante Integration Guide

## Overview

This workflow integrates Zapier with TeamSystem Alyante ERP to enable dynamic inventory thresholds.

## Data Flow

Alyante ERP (source of truth)
    ↓
Export CSV (daily, auto)
    ↓
Server file location: /exports/alyante-inventory.csv
    ↓
Zapier reads file
    ↓
Match SKU + get threshold
    ↓
Compare with Shopify qty

## Alyante Export Configuration

### File Location
Protocol: SFTP or HTTP
Path: /server/exports/alyante-inventory.csv
Frequency: Daily, 6:00 AM
Format: CSV (UTF-8)

### CSV Columns (Required)

SKU | scorta_minima | qty_disponibile | qty_impegnato | qty_fallato | lead_time | moq

### Example Data

12345,15,12,0,0,7,50
12346,30,38,5,0,5,20
12347,50,47,0,0,14,100

## Zapier Configuration

### Step: Read Alyante CSV

App: Zapier (HTTP request)
Method: GET
URL: https://server/exports/alyante-inventory.csv

### Step: Parse CSV & Lookup

App: Zapier (Formatter)
Action: Parse CSV
Input: {{CSV file from Alyante}}
Output: Structured data

### Step: Match SKU

Action: Lookup SKU
Compare: Shopify product SKU
Against: Alyante CSV SKU
Return: scorta_minima value

## Error Handling

If Alyante file unavailable:
- Use fallback threshold (50 for all products)
- Log error to Sheets
- Alert manager

If SKU not found:
- Escalate to manager
- Log in error log
- Manual review required

## Maintenance

- Weekly: Verify CSV format
- Monthly: Check lead times accuracy
- Quarterly: Review threshold effectiveness
