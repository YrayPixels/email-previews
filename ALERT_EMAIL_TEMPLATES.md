# Alertix Email Alert Templates Documentation

## Overview
This document provides a comprehensive overview of all alert types in the Alertix system and the email templates sent to users for each alert type.

**Email Template Used**: `HourlyAlertEmailTemplate` (React Email Component)

**Email Frequency**: Hourly (configurable in alert settings)

**Email Subject Format**: 
```
{subject} - {storeName} - {alertCount} Alert(s) - {date} #{counter}
```

---

## Email Template Structure

### Header Section
- **Logo**: Alertix bell icon (red)
- **Main Heading**: "You have {count} changes on your listings."
- **Subtitle**: "If these changes were made by you, you can safely ignore this email."
- **Account Info**: "Account: [US] {storeName}"

### Alert Item Structure
Each alert in the email includes:
- Product image (100x100px)
- Alert type title (bold)
- Timestamp
- Alert-specific message
- ASIN and SKU information
- "Get Immediate Help" button (for supported alert types)

### Footer Section
- "View Full Details" CTA button → https://app.alertix.ai
- Unsubscribe link → https://alertix.com/unsubscribe
- Copyright notice

---

## Alert Types and Email Messages

### 1. ADULT Flag Alert
**Display Name**: Adult Flag Added  
**Severity**: CRITICAL  
**Support Link**: ✅ Yes - https://link.onlinesellersolutions.com/widget/booking/taL6YxjcaezW3dKl2Nun

#### Email Message (When Flagged):
```
Amazon has flagged ASIN:{asin_value} SKU:{sku_value} as "Adult." 
This may limit visibility and affect advertising eligibility.
```

#### Email Message (When Unflagged):
```
{asin_value} is no longer flagged as "Adult."
```

#### Alert Logic:
- Triggered when `is_adult_product` changes from `false` to `true` or vice versa
- Data source: Keepa API

---

### 2. LISTING_STATUS Alert
**Display Name**: Listing Suppressed  
**Severity**: CRITICAL  
**Support Link**: ✅ Yes - https://link.onlinesellersolutions.com/widget/booking/9FSWOh0uLMaomOPVSe8M

#### Email Message (Inactive/Closed):
```
Listing ASIN:{asin_value} SKU:{sku_value} is now inactive (closed).
```

#### Email Message (No Data Accessible):
```
Listing {asin_value} ({sku_value}) has no price, rating, or offers data accessible.
```

#### Alert Logic:
- Triggered when `product_type` changes to `3` (no data accessible) or `4` (inactive/closed)
- Data source: Amazon SP API (primary) or Keepa API (fallback)

---

### 3. BUY_BOX Alert
**Display Name**: Buy Box Lost  
**Severity**: CRITICAL  
**Support Link**: ✅ Yes - https://link.onlinesellersolutions.com/widget/booking/YLo7d8REKpbZCyJsSdw7

#### Email Message (Lost Buy Box):
```
You've lost the Buy Box for ASIN:{asin_value} SKU:{sku_value}.
```

#### Email Message (Buy Box Suppressed):
```
Buy Box suppressed for ASIN:{asin_value} SKU:{sku_value}.
```

#### Alert Logic:
- Triggered when `buy_box_seller_id_history` changes and current value is `-1` (suppressed) or `-2` (lost)
- Data source: Amazon SP API (primary) or Keepa API (fallback)

---

### 4. MAIN_IMAGE Alert
**Display Name**: Main Image Changed  
**Severity**: MODERATE  
**Support Link**: ❌ No

#### Email Message:
```
The main image for ASIN:{asin_value} SKU:{sku_value} has been updated.
```

#### Alert Logic:
- Triggered when the main product image (first image in array) changes
- Compares `images[0].l` value between previous and current Keepa product data
- Data source: Keepa API

---

### 5. NUMBER_OF_OFFERS Alert
**Display Name**: Number of Offers  
**Severity**: LOW  
**Support Link**: ❌ No

#### Email Message:
```
There are new sellers on ASIN:{asin_value} SKU:{sku_value}. 
This may be a sign of unauthorized sellers.
```

#### Summary Details:
The email summary includes granular offer changes:
- New FBA offers: {previous} → {current}
- New FBM offers: {previous} → {current}
- Used FBA offers: {previous} → {current}
- Used FBM offers: {previous} → {current}
- Collectible FBA offers: {previous} → {current}
- Collectible FBM offers: {previous} → {current}
- Refurbished FBA offers: {previous} → {current}
- Refurbished FBM offers: {previous} → {current}

#### Alert Logic:
- Triggered when `buy_box_eligible_offer_counts` array changes
- Tracks 8 different offer types
- Data source: Keepa API

---

### 6. OUT_OF_STOCK Alert
**Display Name**: Out of Stock  
**Severity**: CRITICAL  
**Support Link**: ✅ Yes - https://link.onlinesellersolutions.com/widget/booking/97YdsaqpdyIvInetTygH

#### Email Message:
```
Listing ASIN:{asin_value} SKU:{sku_value} is currently out of stock.
```

#### Alert Logic:
- Triggered when `buy_box_seller_id_history` changes and current value is `-2` (out of stock)
- Data source: Amazon SP API (primary) or Keepa API (fallback)

---

### 7. TITLE Alert
**Display Name**: Title Changed  
**Severity**: MODERATE  
**Support Link**: ❌ No

#### Email Message:
```
Your product title for ASIN:{asin_value} SKU:{sku_value} has been updated to:
"{currentTitle}"
```

#### Alert Logic:
- Triggered when `title` field changes
- Displays the full new title in italics
- Data source: Keepa API

---

### 8. FEATURES Alert (Bullet Points)
**Display Name**: Bullet Points Changed  
**Severity**: MODERATE  
**Support Link**: ❌ No

#### Email Message:
```
Your bullet points for ASIN:{asin_value} SKU:{sku_value} have been updated to:
```

Followed by a formatted bullet list showing all current features:
- Bold text before colon (if present)
- Regular text after colon
- Each bullet point on a new line

#### Alert Logic:
- Triggered when `features` array changes (order-sensitive)
- Compares arrays case-sensitively with order mattering
- Data source: Keepa API

---

### 9. REVIEW_COUNT Alert
**Display Name**: Review Count Changed  
**Severity**: LOW  
**Support Link**: ❌ No

#### Email Message:
```
The review count for ASIN:{asin_value} SKU:{sku_value} has changed from {previousValue} to {currentValue}.
```

#### Alert Logic:
- Triggered when review IDs in `reviewCount` array change
- Tracks both added and removed reviews
- Summary format: "Added X new review(s)" and/or "Removed X review(s)"
- Data source: Keepa API

---

## Email Design Specifications

### Color Scheme
- **Primary Brand Color**: `#D42A2A` (Red)
- **Background**: `#f4f4f4` (Light gray)
- **Container Background**: `#ffffff` (White)
- **Text Color**: `#333333` (Dark gray)
- **Secondary Text**: `#666666` (Medium gray)
- **Timestamp Color**: `#999999` (Light gray)
- **Border Color**: `#ececec` (Very light gray)

### Typography
- **Font Family**: Arial, sans-serif
- **Main Heading**: 24px, normal weight
- **Subtitle**: 14px
- **Alert Title**: 14px, bold
- **Body Text**: 14px
- **Timestamp**: 12px

### Layout
- **Container Width**: 600px (desktop)
- **Responsive**: Stack layout on mobile (<600px)
- **Product Image**: 100x100px, rounded corners
- **Button Padding**: 14px x 28px (CTA), 8px x 16px (Help)

### Email Headers (Anti-Threading)
To prevent email threading, the following headers are included:
- `Message-ID`: Unique identifier
- `X-Thread-Id`: Unique thread ID
- `References`: Empty
- `In-Reply-To`: Empty
- `X-Gmail-Labels`: "Alertix"
- `X-No-Thread`: "true"

---

## Alert Settings

### Global Settings (per user)
- **Frequency**: HOURLY (default)
- **Channel**: EMAIL (default)
- **Enabled**: true (default)

### SKU-Level Settings
Each SKU can have individual alert type settings that override global settings:
- Individual alert types can be enabled/disabled per SKU
- Settings stored in `alert_sku_settings` table

---

## Data Flow

### Email Creation Process
1. **Trigger**: Automated hourly check via cron/scheduled job
2. **Alert Detection**: System compares current vs. previous product data
3. **Notification Creation**: Alerts are stored in `notifications` table
4. **Email Queueing**: Notifications are grouped by user and prepared for sending
5. **Email Sending**: 
   - Service: Resend API
   - Template: `HourlyAlertEmailTemplate`
   - From: Configured sender email
   - Reply-To: Configured reply-to email
   - CC: Optional (for specific test users)
   - BCC: Optional (for monitoring)

### Data Sources
- **Keepa API**: Primary source for most alerts (title, features, images, reviews, offers, adult flag)
- **Amazon SP API**: Primary source for critical alerts (listing status, buy box, out of stock)

---

## Support Links

The following alert types include a "Get Immediate Help" button:
1. **LISTING_STATUS**: Book consultation for listing issues
2. **ADULT**: Book consultation for adult flag removal
3. **BUY_BOX**: Book consultation for buy box recovery
4. **OUT_OF_STOCK**: Book consultation for stock issues

All support links direct to Online Seller Solutions booking system.

---

## Email Subject Example
```
Alertix: Amazon Listing Alerts - Acme Store - 3 Alerts - Dec 24 #5
```

Components:
- **Base Subject**: "Alertix: Amazon Listing Alerts"
- **Store Name**: From user's `amazon_metadata`
- **Alert Count**: Total number of alerts in this email
- **Date**: Current date in "Mon DD" format (EST)
- **Counter**: Daily email counter for this user (prevents threading)


## Technical Implementation

### Email Template File
`service-alertix/src/tools/resend/templates/HourlyAlertEmailTemplate.tsx`

### Alert Detection Logic
`service-alertix/src/shared/helpers/alert.helper.ts`

### Email Utility
`service-alertix/src/shared/utils/email.util.ts`

### Email Service
`service-alertix/src/features/email/email.service.ts`

### Resend Integration
`service-alertix/src/tools/resend/resend.service.ts`


