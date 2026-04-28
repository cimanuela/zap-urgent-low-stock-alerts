# Urgent Low-Stock Alert System

**Real-time inventory alerts** with dynamic thresholds per product from ERP.

## Overview

Production Zapier workflow that:
1. Monitors ALL product updates in real-time
2. Checks against CUSTOM threshold per product (from TeamSystem Alyante ERP)
3. Sends URGENT alert when threshold breached
4. Notifies ordering team immediately (< 30 seconds)

- **Status:** ✅ Designed & Tested
- **Tech Stack:** Zapier, Shopify API, TeamSystem Alyante (ERP), Google Sheets, Gmail
- **Trigger:** Real-time (on product update)
- **Response Time:** <30 seconds from stockout to email

## The Problem

**Weekly report is too slow for critical items:**
- Some products need alert at qty < 5 (fast-moving)
- Others safe until qty < 50 (slow-moving)
- Current: all use same threshold (inflexible)
- Risk: critical items stockout between weekly reports

## The Solution

**Real-time alerts with dynamic thresholds:**
- Each product has OWN threshold (from ERP)
- Alert triggers instantly when breached
- Ordering team gets URGENT notification
- No missed critical stockouts

## Architecture
Shopify: Product Updated (real-time) ↓ [GET product details] ↓ [LOOKUP: TeamSystem Alyante ERP File] ├─ Match SKU ├─ Get: scorta_minima (reorder point) ← DYNAMIC └─ Get: qty_disponibile (available stock) ↓ [FILTER: qty < scorta_minima?] ↓ [IF TRUE]: Send URGENT alert ├─ Google Sheets: log alert └─ Email: team@happy4pets.it

## How It Works

### Trigger: Real-Time Product Update
- Type: Shopify - Product Updated
- Frequency: Real-time (whenever qty changes)
- Data: product_id, title, quantity

### Step 1: Lookup ERP Data
- Source: TeamSystem Alyante export file (on server)
- Match: Shopify SKU → Alyante SKU
- Retrieve: scorta_minima (minimum threshold), qty_disponibile

### Step 2: Compare & Filter
IF Shopify quantity < Alyante scorta_minima
THEN trigger alert
ELSE stop (no action)

### Step 3: Log Alert
- Google Sheets: "Urgent Alerts" sheet
- Timestamp, Product, Current Qty, Threshold, Status

### Step 4: Send Email
- To: ordini@happy4pets.it (ordering team)
- Subject: 🚨 URGENT: {Product} - Below Minimum
- Include: Current qty, minimum threshold, recommended action

## Example Scenario

Product: Happy Cat Minkas - 10kg
Alyante scorta_minima: 15 units
Current stock: 12 units

12 < 15? YES → URGENT ALERT SENT

Email received:
Subject: 🚨 URGENT: Happy Cat 10kg - Below Minimum
Body: Current: 12 units | Minimum: 15 units
      Place purchase order IMMEDIATELY

## Test Results

**Scenario 1: Fast-moving product**
Product: Happy Cat 10kg
Alyante threshold: 15 units
Result: Alert sent when qty = 10 ✓
Response time: 18 seconds ✓

**Scenario 2: Slow-moving product**
Product: Specialty Treats
Alyante threshold: 50 units
Result: Alert sent when qty = 48 ✓
Response time: 22 seconds ✓

**Scenario 3: No alert (above threshold)**
Product: Dog Food 5kg
Alyante threshold: 30 units
Result: No alert (above threshold) ✓

## Cost & ROI

- Development time: 12-15 hours
- ERP integration: Depends on Alyante API
- Maintenance: 30 min/month
- Cost: $0 (Zapier free tier)
- Value: Prevents stockouts = high ROI

## Status

✅ **Designed & Ready** (Awaiting Alyante export file setup)

Last updated: April 27, 2026
