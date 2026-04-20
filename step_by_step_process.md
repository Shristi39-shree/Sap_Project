# Order-to-Cash (O2C) Complete Sales Cycle - Step-by-Step Process

## Process Overview
The Order-to-Cash (O2C) cycle represents the complete business process from receiving a customer order to collecting payment. This document outlines each step with SAP SD transaction codes and configuration requirements.

---

## PHASE 1: PRE-SALES ACTIVITIES

### Step 1: Organization Structure Setup

#### 1.1 Create Company Code
**Transaction:** OX02  
**Path:** SPRO → Enterprise Structure → Definition → Financial Accounting → Define Company Code

**Configuration Steps:**
1. Click on "New Entries"
2. Enter Company Code: **TG01**
3. Company Name: **TechGear Industries Pvt. Ltd.**
4. City: Mumbai
5. Country: IN (India)
6. Currency: INR
7. Language: EN
8. Save the configuration

#### 1.2 Create Sales Organization
**Transaction:** OVX5  
**Path:** SPRO → Enterprise Structure → Definition → Sales and Distribution → Define Sales Organization

**Configuration Steps:**
1. New Entries
2. Sales Organization: **TG01**
3. Name: **TechGear Sales India**
4. Address details
5. Assign to Company Code TG01
6. Save

#### 1.3 Create Distribution Channel
**Transaction:** OVXK  
**Path:** SPRO → Enterprise Structure → Definition → Sales and Distribution → Define Distribution Channel

**Channels Created:**
- **01** - Direct Sales
- **02** - Wholesale Distribution
- **03** - E-commerce

#### 1.4 Create Division
**Transaction:** OVXK  
**Path:** SPRO → Enterprise Structure → Definition → Sales and Distribution → Define Division

**Divisions:**
- **01** - Computer Hardware
- **02** - Computer Peripherals
- **03** - Networking Equipment

#### 1.5 Create Sales Area
**Sales Area = Sales Organization + Distribution Channel + Division**

Combinations:
- TG01 / 01 / 01 (Direct Sales - Computer Hardware)
- TG01 / 02 / 01 (Wholesale - Computer Hardware)
- TG01 / 03 / 02 (E-commerce - Peripherals)

#### 1.6 Create Plant
**Transaction:** OX10  
**Path:** SPRO → Enterprise Structure → Definition → Logistics General → Define Plant

**Plants:**
- **TG01** - Mumbai Manufacturing Plant
- **TG02** - Delhi Distribution Center
- **TG03** - Bangalore Warehouse

#### 1.7 Create Storage Location
**Transaction:** OX09  
**Path:** SPRO → Enterprise Structure → Definition → Materials Management → Define Storage Location

**Storage Locations per Plant:**
- 0001 - Raw Materials
- 0002 - Finished Goods
- 0003 - Quality Control Hold

---

## PHASE 2: MASTER DATA CREATION

### Step 2: Customer Master Data

#### 2.1 Create Customer Master (XD01)
**Transaction:** XD01 (Create), XD02 (Change), XD03 (Display)

**Customer Types:**
1. **Sold-to Party** - Main customer who orders
2. **Ship-to Party** - Goods delivery location
3. **Bill-to Party** - Invoice recipient
4. **Payer** - Payment maker

**Sample Customer 1:**
```
Customer Number: 100001
Name: Retail Tech Solutions Pvt. Ltd.
Account Group: KUNA (Sold-to Party)
Sales Organization: TG01
Distribution Channel: 01
Division: 01

General Data:
- Address: 123 MG Road, Bangalore 560001
- Country: India
- Region: Karnataka
- Telephone: +91-80-12345678
- Email: orders@retailtech.com

Company Code Data (TG01):
- Reconciliation Account: 140000
- Payment Terms: 0030 (Net 30 days)
- Payment Methods: T (Bank Transfer), C (Cheque)

Sales Area Data (TG01/01/01):
- Currency: INR
- Price Group: 01
- Customer Pricing Procedure: A (Standard)
- Order Probability: 100%
- Delivering Plant: TG01
- Shipping Conditions: 01 (Standard Shipping)
- Incoterms: CIF (Cost, Insurance & Freight)
- Payment Terms: 0030
- Tax Classification: 1 (Taxable)
```

**Sample Customer 2:**
```
Customer Number: 100002
Name: Enterprise Systems India Ltd.
Account Group: KUNA
Sales Organization: TG01
Distribution Channel: 02
Division: 01

Sales Area Data:
- Price Group: 02 (Wholesale Pricing)
- Delivering Plant: TG02
- Payment Terms: 0014 (Net 14 days)
- Incoterms: FOB (Free on Board)
```

### Step 3: Material Master Data

#### 3.1 Create Material Master (MM01)
**Transaction:** MM01 (Create), MM02 (Change), MM03 (Display)

**Material Type:** FERT (Finished Product)

**Sample Material 1:**
```
Material Number: 100001
Material Type: FERT
Industry Sector: M (Mechanical Engineering)
Description: High-Performance Gaming Laptop

Basic Data 1:
- Base Unit of Measure: EA (Each)
- Material Group: COMP001 (Computers)
- Division: 01

Sales: Sales Org. 1 (TG01/01):
- Sales Unit: EA
- Delivering Plant: TG01
- Tax Classification: 1 (MWST: Full Tax)
- Item Category Group: NORM (Normal Item)
- Account Assignment Group: 01

Sales: Sales Org. 2 (TG01/02):
- Price Group: 02
- Material Pricing Group: 01

Sales: General/Plant Data:
- Transportation Group: 0001
- Loading Group: 0001
- Availability Check: 02 (Individual Requirements)

MRP 1:
- MRP Type: PD (MRP)
- MRP Controller: 001
- Lot Size: EX (Lot-for-Lot)
- Procurement Type: E (In-house Production)

MRP 2:
- Procurement Type: E
- Planned Delivery Time: 2 days
- GR Processing Time: 1 day

Accounting 1:
- Valuation Class: 7920
- Price Control: S (Standard Price)
- Standard Price: ₹75,000
- Currency: INR

Costing 1:
- Material Origin: 1
```

**Sample Material 2:**
```
Material Number: 100002
Description: Wireless Gaming Mouse
Material Type: FERT
Base Unit: EA
Material Group: PERI001 (Peripherals)
Division: 02
Standard Price: ₹2,500
```

**Sample Material 3:**
```
Material Number: 100003
Description: 24-Port Gigabit Network Switch
Material Type: FERT
Base Unit: EA
Material Group: NETW001 (Network Equipment)
Division: 03
Standard Price: ₹18,000
```

### Step 4: Pricing Configuration

#### 4.1 Create Condition Types
**Transaction:** V/06  
**Path:** SPRO → Sales and Distribution → Basic Functions → Pricing → Pricing Control → Define Condition Types

**Condition Types:**
```
PR00 - Base Price (Mandatory)
K004 - Material Price (Optional)
K005 - Customer Price (Optional)
K007 - Material Group Price
ZD01 - Volume Discount (%)
ZD02 - Customer Discount (%)
MWST - Output Tax (GST 18%)
SKTO - Cash Discount (2/10 Net 30)
KF00 - Freight Charges
```

#### 4.2 Create Condition Tables
**Transaction:** V/05  

**Condition Tables Created:**
- Table 001: Customer/Material
- Table 002: Material
- Table 003: Customer
- Table 004: Customer/Material Group

#### 4.3 Create Access Sequence
**Transaction:** V/07  

**Access Sequence ZSTA01:**
1. Access 10: Customer/Material (Table 001)
2. Access 20: Material (Table 002)
3. Access 30: Customer (Table 003)

#### 4.4 Define Pricing Procedure
**Transaction:** V/08  

**Pricing Procedure A00001 (Standard Pricing):**
```
Step | Condition Type | Description              | From | To  | Manual | Subtotal
-----|----------------|--------------------------|------|-----|--------|----------
10   | PR00          | Base Price               |      |     | X      | A
20   | ZD01          | Volume Discount          | 10   | 10  | X      | B
30   | ZD02          | Customer Discount        | 20   | 20  | X      | C
40   | K004          | Material Price           |      |     |        |
50   |               | Net Value 1              |      | 40  |        | D
60   | KF00          | Freight                  |      |     | X      |
70   |               | Net Value 2              |      | 60  |        | E
80   | MWST          | GST @ 18%                | 70   | 70  |        |
90   | SKTO          | Cash Discount            | 70   | 70  | X      |
100  |               | Total Price              |      | 90  |        | F
```

#### 4.5 Maintain Pricing Records
**Transaction:** VK11 (Create), VK12 (Change), VK13 (Display)

**Sample Pricing:**
```
Condition Type: PR00
Material: 100001 (Gaming Laptop)
Amount: ₹85,000 per EA
Valid From: 01.01.2026
Valid To: 31.12.2026

Condition Type: ZD01
Material: 100001
Scale Quantity: 1-5 EA → 0%
Scale Quantity: 6-10 EA → 5%
Scale Quantity: 11+ EA → 10%

Condition Type: MWST
Tax Code: IN
GST Rate: 18%
```

---

## PHASE 3: ORDER-TO-CASH PROCESS EXECUTION

### Step 5: Sales Inquiry (Optional)

#### 5.1 Create Sales Inquiry
**Transaction:** VA11  
**Document Type:** IN (Inquiry)

**Process:**
1. Enter Transaction VA11
2. Inquiry Type: IN
3. Sales Organization: TG01
4. Distribution Channel: 01
5. Division: 01
6. Sales Office: (Optional)

**Header Data:**
- Sold-to Party: 100001
- Inquiry Date: Current Date
- Valid Until: +30 days

**Item Data:**
- Material: 100001
- Quantity: 10 EA
- Plant: TG01

7. Save (Document Number generated: e.g., 10000001)

**Transaction Codes:**
- VA11 - Create Inquiry
- VA12 - Change Inquiry
- VA13 - Display Inquiry

### Step 6: Sales Quotation

#### 6.1 Create Sales Quotation
**Transaction:** VA21  
**Document Type:** QT (Quotation)

**Process:**
1. Enter VA21
2. Quotation Type: QT
3. Sales Org: TG01 / Dist. Channel: 01 / Division: 01

**Header Data:**
- Sold-to Party: 100001
- PO Number: PO-2026-001 (Customer's Purchase Order)
- Quotation Valid From: Current Date
- Quotation Valid To: Current Date + 30 days

**Item Overview:**
```
Item | Material | Description        | Qty | Unit | Plant | Price
-----|----------|-------------------|-----|------|-------|--------
10   | 100001   | Gaming Laptop     | 10  | EA   | TG01  | 85,000
20   | 100002   | Wireless Mouse    | 10  | EA   | TG01  | 2,500
```

**Pricing Screen (Conditions Tab):**
- Base Price (PR00): ₹85,000
- Volume Discount (ZD01): -5% (₹4,250) [6-10 units]
- Net Price: ₹80,750
- Freight (KF00): ₹500
- Subtotal: ₹81,250
- GST 18% (MWST): ₹14,625
- Total: ₹95,875

4. Check ATP (Available-to-Promise): Material Availability Check
5. Schedule Lines Tab: Confirms delivery dates
6. Save (Quotation Number: e.g., 20000001)

**Transaction Codes:**
- VA21 - Create Quotation
- VA22 - Change Quotation
- VA23 - Display Quotation

### Step 7: Sales Order Creation

#### 7.1 Create Sales Order with Reference to Quotation
**Transaction:** VA01  
**Document Type:** OR (Standard Order)

**Method 1: Create from Quotation**
1. Enter VA01
2. Order Type: OR
3. Click "Create with Reference" button
4. Enter Quotation Number: 20000001
5. Press Enter

**Method 2: Create Directly**
1. Enter VA01
2. Order Type: OR
3. Sales Org: TG01 / Dist. Ch.: 01 / Division: 01

**Header Data:**
- Sold-to Party: 100001 (Retail Tech Solutions)
- Ship-to Party: 100001 (Same or different)
- Bill-to Party: 100001
- Payer: 100001
- PO Number: PO-2026-001
- PO Date: Current Date
- Requested Delivery Date: Current Date + 7 days
- Payment Terms: 0030 (Net 30 days)
- Incoterms: CIF Mumbai

**Item Data:**
```
Item | Material | Description        | Order Qty | Unit | Plant | Delivery Date
-----|----------|--------------------|-----------|------|-------|---------------
10   | 100001   | Gaming Laptop      | 10        | EA   | TG01  | Current+7
20   | 100002   | Wireless Mouse     | 10        | EA   | TG01  | Current+7
```

**Conditions Tab (Item Level):**
- View automatically determined pricing
- Manual adjustments possible
- Total Order Value verification

**Schedule Line Tab:**
- Confirms material availability
- Shows planned delivery dates
- ATP check results

**Partners Tab:**
- Sold-to Party: 100001
- Ship-to Party: 100001
- Bill-to Party: 100001
- Payer: 100001

**Item Details:**
- Material Group: COMP001
- Item Category: TAN (Standard Item)
- Shipping Point: TG01 (determined automatically)

4. **Credit Check:** 
   - Menu → Edit → Credit Check
   - System performs automatic credit limit check
   - Status: Released / Blocked

5. **Incompletion Log:**
   - Menu → Edit → Incompletion Log
   - Check for missing mandatory data

6. **Save Order:**
   - Click Save
   - Sales Order Number generated: e.g., **40000001**
   - Note: Document flow updated

**Transaction Codes:**
- VA01 - Create Sales Order
- VA02 - Change Sales Order
- VA03 - Display Sales Order
- VA05 - List of Sales Orders

#### 7.2 Check Document Flow
**Transaction:** VA03

1. Enter Sales Order Number: 40000001
2. Menu → Environment → Document Flow (or F5)

**Document Flow View:**
```
Inquiry 10000001 → Quotation 20000001 → Sales Order 40000001
```

---

## PHASE 4: DELIVERY PROCESSING

### Step 8: Outbound Delivery Creation

#### 8.1 Create Outbound Delivery
**Transaction:** VL01N  
**Document Type:** LF (Delivery)

**Process:**
1. Enter VL01N
2. Shipping Point: TG01
3. Selection Date: Current Date
4. Click on "Outbound Delivery" tab

**Create Delivery - Sales Order Reference:**
- Sales Order Number: 40000001
- Press Enter

**Delivery Header:**
- Ship-to Party: 100001
- Actual GI Date: Current Date
- Route: (Auto-determined)
- Shipping Type: 01
- Weight: (Auto-calculated)

**Item Overview:**
```
Item | Material | Description        | Deliv.Qty | Unit | Storage Loc | Batch
-----|----------|--------------------|-----------|------|-------------|-------
10   | 100001   | Gaming Laptop      | 10        | EA   | 0002        |
20   | 100002   | Wireless Mouse     | 10        | EA   | 0002        |
```

5. Save Delivery Document
   - Delivery Number generated: e.g., **80000001**

**Transaction Codes:**
- VL01N - Create Outbound Delivery
- VL02N - Change Outbound Delivery
- VL03N - Display Outbound Delivery
- VL06O - Outbound Delivery Monitor

#### 8.2 Picking Process

**Transaction:** VL02N (Change Delivery)

**Process:**
1. Enter Delivery Number: 80000001
2. Menu → Logistics → Picking
3. Or click Picking button

**Picking Methods:**
- **Manual Picking:** Warehouse staff picks items manually
- **Wave Picking:** Batch picking for multiple orders
- **Automatic Picking:** System confirms automatically

**Picking Confirmation:**
- Item 10: Picked Quantity: 10 EA (Confirmed)
- Item 20: Picked Quantity: 10 EA (Confirmed)

4. Save

#### 8.3 Packing (Optional)

**Transaction:** VL02N

**Process:**
1. Menu → Subsequent Functions → Packing
2. Create Handling Unit
3. Packing Material: Carton Box (PACK001)
4. Number of Packages: 2
5. Pack Items into Handling Units
6. Save

### Step 9: Post Goods Issue (PGI)

#### 9.1 Post Goods Issue
**Transaction:** VL02N

**Process:**
1. Open Delivery: 80000001
2. Check all items are picked
3. Click "Post Goods Issue" button
4. Enter Actual GI Date: Current Date

**System Actions:**
- Material Document created (MM movement type 601)
- Stock reduced from Plant TG01, Storage Location 0002
- Inventory updated
- Financial documents updated
- Delivery status: Completed

5. Goods Issue completed successfully
   - Material Document: 4900000001 (Year 2026)
   - Document Date: Current Date

**Transaction Codes:**
- VL06G - List of Deliveries for Goods Issue
- MB03 - Display Material Document

#### 9.2 Verify Document Flow

**Transaction:** VA03
1. Enter Sales Order: 40000001
2. Press F5 (Document Flow)

**Updated Flow:**
```
Inquiry 10000001
  ↓
Quotation 20000001
  ↓
Sales Order 40000001
  ↓
Outbound Delivery 80000001 (Status: Completed)
  ↓
Material Document 4900000001
```

---

## PHASE 5: BILLING

### Step 10: Invoice Creation

#### 10.1 Create Billing Document
**Transaction:** VF01  
**Document Type:** F2 (Invoice)

**Process:**
1. Enter VF01
2. Billing Type: F2 (Invoice)
3. Click on "Billing Due List" or enter delivery number directly

**Create Invoice with Reference:**
- Delivery Number: 80000001
- Billing Date: Current Date
- Press Enter

**Billing Document Header:**
- Sold-to Party: 100001
- Bill-to Party: 100001
- Billing Date: Current Date
- Payer: 100001
- Payment Terms: 0030 (Net 30 days)

**Item Overview:**
```
Item | Material | Description        | Billed Qty | Unit | Net Value  | Tax    | Total
-----|----------|--------------------|------------|------|------------|--------|--------
10   | 100001   | Gaming Laptop      | 10         | EA   | ₹8,07,500  |₹1,45,350| ₹9,52,850
20   | 100002   | Wireless Mouse     | 10         | EA   | ₹25,000    |₹4,500   | ₹29,500
                                                   TOTAL | ₹8,32,500  |₹1,49,850| ₹9,82,350
```

**Pricing Details:**
- Subtotal (without tax): ₹8,32,500
- GST @ 18%: ₹1,49,850
- Freight: ₹500 (included)
- **Grand Total: ₹9,82,350**

3. Check for errors (Menu → Edit → Incompletion Log)
4. Save Invoice
   - Invoice Number generated: e.g., **90000001**
   - Accounting Document automatically created

**Transaction Codes:**
- VF01 - Create Billing Document
- VF02 - Change Billing Document
- VF03 - Display Billing Document
- VF04 - Billing Due List

#### 10.2 View Accounting Document

**Transaction:** FB03 (Display Document)

**Process:**
1. Enter Invoice Number in VF03
2. Menu → Environment → Document Flow → Accounting Document
3. Or use Transaction FB03

**Accounting Entry:**
```
Posting Date: Current Date
Document Type: RV (Billing Document)
Company Code: TG01

Account                              | Description              | Debit      | Credit
-------------------------------------|--------------------------|------------|------------
140000 (Customer Receivables)        | Customer 100001          | ₹9,82,350  |
800000 (Revenue Account)             | Sales Revenue            |            | ₹8,32,500
155000 (GST Receivable - Output Tax) | GST Payable              |            | ₹1,49,850
```

4. Document is balanced (Debit = Credit = ₹9,82,350)

---

## PHASE 6: PAYMENT COLLECTION

### Step 11: Incoming Payment

#### 11.1 Post Customer Payment
**Transaction:** F-28 (Post Incoming Payment)

**Process:**
1. Enter F-28
2. Document Date: Current Date (Payment received date)
3. Posting Date: Current Date
4. Company Code: TG01
5. Currency: INR

**Bank Data:**
- Account: 100000 (Main Bank Account)
- Amount: ₹9,82,350
- Value Date: Current Date
- Bank Charges: ₹0 (if any)

**Customer Data:**
- Account: 100001 (Retail Tech Solutions)
- Amount: ₹9,82,350
- Payment Reference: Customer's transaction reference

**Open Item Selection:**
- Select Invoice 90000001
- Amount: ₹9,82,350
- Click "Process Open Items"

**Clearing:**
- System automatically clears the invoice
- Remaining balance: ₹0

7. Post Document
   - Clearing Document Number: e.g., 19000001
   - Payment cleared successfully

**Transaction Codes:**
- F-28 - Post Incoming Payment
- FBL5N - Customer Line Items Display
- F-32 - Clear Customer Account

#### 11.2 Verify Customer Account

**Transaction:** FBL5N

**Process:**
1. Enter Customer Number: 100001
2. Company Code: TG01
3. Open Items: Unchecked (to see all items)
4. Execute

**Customer Account Display:**
```
Document | Doc Type | Posting Date | Amount      | Status
---------|----------|--------------|-------------|--------
90000001 | RV       | [Date]       | ₹9,82,350   | Cleared
19000001 | DZ       | [Date]       | -₹9,82,350  | Cleared
```

**Status:** Account balanced, no open items

---

## PHASE 7: POST-SALES ACTIVITIES

### Step 12: Returns Processing (If Applicable)

#### 12.1 Create Return Sales Order
**Transaction:** VA01  
**Document Type:** RE (Returns)

**Process:**
1. Enter VA01
2. Order Type: RE
3. Sales Org: TG01 / Dist. Ch: 01 / Div: 01

**Create with Reference:**
- Reference to Invoice: 90000001
- Or enter manually

**Header Data:**
- Sold-to Party: 100001
- Reason for Rejection: 01 (Defective Material)
- Return Material Authorization (RMA) Number

**Item Data:**
```
Item | Material | Description     | Return Qty | Reason
-----|----------|-----------------|------------|--------
10   | 100001   | Gaming Laptop   | 2          | 01
```

4. Save Return Order
   - Return Order Number: e.g., 41000001

#### 12.2 Create Return Delivery
**Transaction:** VL01N

**Process:**
1. Reference Return Order: 41000001
2. Create Inbound Delivery
3. Post Goods Receipt (Movement Type 651)

#### 12.3 Create Credit Memo
**Transaction:** VF01  
**Document Type:** G2 (Credit Memo)

**Process:**
1. Reference Return Delivery
2. Create Credit Memo
3. Credit Memo Number: e.g., 91000001
4. Amount credited back to customer

---

## PHASE 8: REPORTING & ANALYTICS

### Step 13: Standard Reports

#### 13.1 Sales Order Report
**Transaction:** VA05

**Selection Criteria:**
- Sales Organization: TG01
- Sold-to Party: 100001
- Created On: Date range
- Execute

**Output:** List of all sales orders

#### 13.2 Delivery Report
**Transaction:** VL06O

**Selection:**
- Shipping Point: TG01
- Delivery Date: Date range
- Execute

#### 13.3 Billing Report
**Transaction:** VF05

**Selection:**
- Billing Type: F2
- Billing Date: Date range
- Sold-to Party: 100001
- Execute

#### 13.4 Customer Account Statement
**Transaction:** FD10N

**Selection:**
- Customer: 100001
- Company Code: TG01
- Date Range
- Execute

**Output:** Complete account statement with all transactions

### Step 14: Document Flow Analysis

**Transaction:** VA03 (Display Sales Order)

**Complete Document Flow:**
```
Pre-Sales:
  - Inquiry 10000001
  - Quotation 20000001

Sales Order:
  - Sales Order 40000001

Delivery:
  - Outbound Delivery 80000001
  - Picking Document
  - Material Document 4900000001 (Goods Issue)

Billing:
  - Invoice 90000001
  - Accounting Document (FI)

Payment:
  - Clearing Document 19000001

Optional Returns:
  - Return Order 41000001
  - Return Delivery
  - Credit Memo 91000001
```

---

## SUMMARY OF KEY TRANSACTION CODES

### Master Data
- XD01/02/03 - Customer Master
- MM01/02/03 - Material Master
- VK11/12/13 - Pricing Conditions

### Sales Documents
- VA11/12/13 - Inquiry
- VA21/22/23 - Quotation
- VA01/02/03 - Sales Order
- VA05 - Sales Order List

### Delivery
- VL01N/02N/03N - Outbound Delivery
- VL06O - Outbound Delivery Monitor

### Billing
- VF01/02/03 - Billing Document
- VF04 - Billing Due List
- VF05 - Billing List

### Finance
- F-28 - Incoming Payment
- FBL5N - Customer Line Items
- FB03 - Display Document

### Reports
- VA05 - Sales Orders
- VL06O - Deliveries
- VF05 - Billing Documents
- FD10N - Customer Account

---

## INTEGRATION POINTS

### SD-MM Integration
- Availability Check (ATP)
- Goods Issue posting
- Inventory management

### SD-FI Integration
- Invoice creation → Accounting document
- Revenue recognition
- Customer receivables
- GST accounting

### SD-PP Integration (If manufacturing)
- Sales order triggers production
- Make-to-Order scenarios

---

## BEST PRACTICES

1. **Always check ATP** before confirming delivery dates
2. **Verify pricing** at every stage
3. **Complete incompletion logs** before saving documents
4. **Use document flow** to track progress
5. **Perform credit checks** for new customers
6. **Maintain accurate master data**
7. **Follow standard operating procedures**
8. **Document all customizations**
9. **Test thoroughly** before go-live
10. **Train users** on standard processes

---

## TROUBLESHOOTING COMMON ISSUES

### Issue 1: ATP Check Fails
**Solution:** Check material availability in MMBE, verify MRP settings

### Issue 2: Pricing Not Determined
**Solution:** Check condition records in VK13, verify pricing procedure assignment

### Issue 3: Delivery Creation Error
**Solution:** Check shipping point determination, verify route determination

### Issue 4: Invoice Block
**Solution:** Check billing block in sales order, verify delivery completion

### Issue 5: Payment Clearing Issues
**Solution:** Verify amounts match, check payment terms, ensure invoice is posted

---

## CONCLUSION

This step-by-step process demonstrates the complete Order-to-Cash cycle in SAP SD, covering all stages from pre-sales activities through payment collection. The process integrates seamlessly with FI and MM modules, ensuring accurate financial postings and inventory management.

**Process Duration:** Typically 7-30 days depending on:
- Order complexity
- Material availability
- Delivery logistics
- Payment terms
- Customer creditworthiness

**Success Metrics:**
- Order Fulfillment Rate: >95%
- On-Time Delivery: >90%
- Invoice Accuracy: >99%
- Days Sales Outstanding (DSO): <30 days
- Customer Satisfaction: >85%
