# The $120,000 Technical Business Analyst & Product Owner Hands-On Masterclass
## A Practical, Step-by-Step Production Workbook

> **Candidate & Practitioner:** Benjamin Banansos  
> **Target Market:** Toronto · Ottawa · Canadian Remote  
> **Target Compensation:** $115,000 – $130,000 CAD (Full-Time Base) or $65 – $85 / Hour (Contract)  
> **Core Objective:** To equip you with the exact day-to-day hands-on deliverables, Jira templates, API inspection techniques, SQL validation scripts, and UAT matrices required to perform and interview at a Senior Technical BA level.

---

# 🧭 Why This Masterclass Exists

At the **$120,000 salary level**, hiring managers and engineering directors do not want someone who merely takes notes in meetings. They want a **Technical Business Analyst** who can:
1. Talk to a messy client or executive and extract crystal-clear business logic.
2. Translate that logic into **developer-ready technical user stories** that eliminate ambiguity.
3. Open an API tool (like Postman) or a database (SQL) and verify data flows without waiting for developers.
4. Catch edge-case bugs and calculation rounding errors **before** code reaches production.
5. Protect the engineering team from chaotic scope creep while keeping stakeholders happy.

This workbook is organized into **6 Hands-On Labs**. Each lab provides **production-ready templates, real-world examples from your portfolio (Janal, SmartDecor, Go Digitally), and practical exercises** you can practice right now.

---

# Lab 1: Requirements Elicitation & Process Mapping
*Turning a Client's Vague Brain-Dump into a Technical Data Flow*

### The Real-World Scenario (Janal.ca / Shades4U)
A non-technical store owner says:  
> *"We get 50 calls a day from people wanting custom blinds. Our sales reps have to ask for window width, height, fabric grade, and motorization, manually look up a paper price grid, calculate tax, and call them back 2 days later. By then, half the customers have bought from Home Depot. We want them to buy directly on the website, but the math has to be accurate down to 1/8th of an inch!"*

---

### Step 1: The Technical BA Discovery Framework (The 5 Core Questions)
When conducting a discovery interview, ask these five questions to uncover hidden edge cases:
1. **The Happy Path:** *"What does the ideal, successful transaction look like from start to finish?"*
2. **The Boundary Constraints:** *"What is the absolute smallest and largest window your factory machines can cut?"* (e.g., Min: 12" wide; Max: 110" wide).
3. **The Precision Rules:** *"How do you handle fractions? Do you round up or down? What happens if someone types 72.13 instead of 72 1/8?"*
4. **The Exception / Failure Path:** *"What happens if a customer enters a dimension that is out of stock or requires special commercial brackets?"*
5. **The Downstream Handshake:** *"Once they click 'Pay', where does the order need to go? Who gets an email, and what data does the factory cut-sheet need?"*

---

### Step 2: Drawing the Technical Data Flow Diagram (BPMN / Swimlane)
As a $120k Technical BA, you never leave requirements as plain paragraphs. You draw a clear data flow. 

```
[CUSTOMER FRONTEND]        [DYNAMIC PRICING ENGINE]       [DATABASE & GATEWAY]
        │                                 │                              │
        │ 1. Enters: 35 1/8" W x 60" H    │                              │
        │    Selects: Grade B Fabric      │                              │
        ├────────────────────────────────►│                              │
        │                                 │ 2. Parses fraction (0.125)   │
        │                                 │    Calculates Sq. Inches     │
        │                                 │    Queries pricing matrix    │
        │                                 │                              │
        │ 3. Displays: $184.50 live       │                              │
        │◄────────────────────────────────┤                              │
        │                                 │                              │
        │ 4. Clicks "Checkout" ($184.50)  │                              │
        ├─────────────────────────────────┴─────────────────────────────►│
        │                                                                │ 5. Creates Order #9102
        │                                                                │    Generates HPP Session
        │                                                                │    Calls Converge API
        │ 6. Renders Bank Hosted Form     │                              │
        │◄───────────────────────────────────────────────────────────────┤
```

---

# Lab 2: Writing $120k-Grade Technical User Stories (Gherkin BDD)
*Eliminating Developer Confusion with Acceptance Criteria*

A junior BA writes vague tickets like:  
> ❌ *"Build the pricing calculator for blinds and make sure fractions work."*  
> (Developers hate this because they have to guess the rules, resulting in bugs).

A **$120,000 Technical BA** writes user stories using **Behavior-Driven Development (BDD)** and **Gherkin Syntax (`Given-When-Then`)**.

---

### Production Ticket Template (Copy & Adapt for Your Projects)

```markdown
### JIRA TICKET: JNL-142
**Epic:** Custom Manufacturing eCommerce & Dynamic Sizing Engine  
**Title:** Real-Time Dimensional Pricing Calculation for Custom Roller Shades  
**Story Type:** User Story  
**Priority:** High (Launch Blocker)  

#### 1. User Story Statement
As an **online custom blinds shopper**,  
I want **the product page to instantly calculate the total price when I enter my window dimensions in inches and fractions**,  
So that **I can see my exact custom price in real-time and checkout without waiting for a manual phone estimate.**

---

#### 2. Business Logic & Calculation Formula
* **Square Inch Formula:** Width (in decimals) * Height (in decimals) = Total Sq Inches
* **Pricing Lookup Table:**
  * Minimum Billing Base: 1,200 sq inches (Orders smaller than 1,200 sq in are billed at the 1,200 base rate).
  * Grade A Fabric: $0.065 / sq in
  * Grade B Fabric: $0.082 / sq in
  * Grade C Fabric (Blackout): $0.098 / sq in
* **Motorization Add-On:** +$110.00 flat fee per unit.
* **Precision Rule:** All final monetary values must round using ROUND_HALF_UP to 2 decimal places.

---

#### 3. Technical Acceptance Criteria (Gherkin BDD Scenarios)

##### Scenario 1: Standard Fractional Dimension (Happy Path)
* **Given** a user is on the Roller Shades product page,
* **When** they enter Width = `35`, Fraction = `1/8` (0.125) and Height = `60`,
* **And** they select `Grade B Fabric` with `No Motorization`,
* **Then** the engine must compute:
  * Width in decimals = 35.125"
  * Sq Inches = 35.125 * 60 = 2107.5 sq in
  * Price = 2107.5 * $0.082 = $172.815 -> $172.82
* **And** update the displayed price to `"$172.82"` within 100ms without page reload.

##### Scenario 2: Minimum Dimension Floor Rule
* **Given** a user enters Width = `15"` and Height = `20"` (Total = 300 sq in),
* **When** the calculation executes,
* **Then** the engine must apply the Minimum Billing Base of 1,200 sq in,
* **And** display an informational tooltip: *"Minimum manufacturing base rate applied."*

##### Scenario 3: Out-of-Bounds Dimension Validation (Negative Path)
* **Given** a user enters a Width greater than `110"` (the factory roll limit),
* **When** the input field loses focus (`onBlur`),
* **Then** the system must disable the "Add to Cart" button,
* **And** display an inline validation error: *"Width exceeds factory limit of 110 inches. Please contact commercial sales for custom engineering."*

---

#### 4. Data Dictionary & Field Specifications
| UI Field Name | Technical Key | Data Type | Validation Rules |
| :--- | :--- | :--- | :--- |
| `width_whole` | `dim_w_whole` | Integer | Min: 12, Max: 110. Mandatory. |
| `width_fraction` | `dim_w_frac` | String / Enum | Allowed: ["0", "1/8", "1/4", "3/8", "1/2", "5/8", "3/4", "7/8"] |
| `height_whole` | `dim_h_whole` | Integer | Min: 16, Max: 120. Mandatory. |
| `calculated_price` | `unit_price` | Decimal(10,2) | Stored as integer cents in backend database ($172.82 -> 17282). |
```

---

# Lab 3: API Inspection, Data Mapping & Postman Hands-On
*Speaking the Developers' Language Without Writing the Code*

As a Technical BA, when the frontend team says: *"The checkout is broken,"* you don't just file a ticket saying *"checkout broken."*  
You open **Postman** (or browser DevTools), inspect the API call, and diagnose the exact issue.

---

### Step 1: Understanding an API Request & Response
An API integration consists of 4 parts:
1. **Method:** `POST` (sending data), `GET` (fetching data), `PUT` (updating data).
2. **Endpoint URL:** `https://api.convergepay.com/hosted-payments/transaction_token`
3. **Headers:** Metadata like `Content-Type: application/json` and `Authorization: Bearer <token>`.
4. **Body (Payload):** The actual JSON data being transmitted.

---

### Step 2: The Real-World Data Mapping Dictionary (Janal <-> Converge Gateway)
You create an **API Data Mapping Sheet** so developers know exactly how frontend fields match the external payment gateway:

| Storefront Field (Janal) | Type | Gateway Parameter (Converge HPP) | Gateway Requirement | Description |
| :--- | :--- | :--- | :--- | :--- |
| Order ID (`#9102`) | String | `ssl_invoice_number` | Required (Max 25 chars) | Unique tracking number for the merchant. |
| Total Price (`$172.82`) | String | `ssl_amount` | Required (Format: `0.00`) | Exact charge amount. |
| Customer Email | String | `ssl_email` | Optional | Used by gateway to send receipt. |
| Server Outbound IP | String | `ssl_merchant_ip` | **Whitelisted IP Only** | Must match AWS Elastic IP registered in Elavon portal. |

---

### Step 3: Diagnosing the `401 Unauthorized` Payment Defect
Here is the exact hands-on JSON payload and response you inspect in Postman:

#### The API Request Sent:
```http
POST /hosted-payments/transaction_token HTTP/1.1
Host: api.convergepay.com
Content-Type: application/json
Authorization: Bearer sec_live_98a7bc8102

{
  "ssl_merchant_id": "008912",
  "ssl_user_id": "api_janal_prod",
  "ssl_amount": "172.82",
  "ssl_invoice_number": "JNL-9102"
}
```

#### The Failed API Response (What You Inspect):
```json
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "error_code": "AUTH_401_IP_REJECTED",
  "message": "Originating IP 54.210.88.14 is not in the merchant whitelisted IP pool.",
  "timestamp": "2024-03-12T14:22:01Z"
}
```

#### How a $120k Technical BA Logs This Bug Ticket:
> **Defect Title:** `[Payment Gateway] Checkout 401 Unauthorized: AWS EC2 Dynamic IP not whitelisted in Converge`  
> **Severity:** Critical (P1 - Blocks Checkout)  
> **Root Cause:** The new AWS EC2 production instance outbound IP (`54.210.88.14`) has not been associated with our static AWS Elastic IP (`3.98.142.10`), causing the Converge firewall to reject the handshake.  
> **Steps to Resolve:**  
> 1. DevOps: Associate AWS Elastic IP `3.98.142.10` with EC2 instance `i-098abc`.  
> 2. Business Lead: Verify IP whitelist status in Elavon Merchant Portal under Terminal Settings.

---

# Lab 4: SQL & Data Verification Hands-On
*Auditing Systems and Verifying Data Like a Pro*

A regular BA waits 3 days for an engineer to run a database report. A **Technical BA** opens a database tool (DBeaver, pgAdmin) and writes their own SQL queries to audit transactions and verify that customer orders match financial gateway records.

---

### 4 Essential SQL Queries Every Technical BA Uses

#### Query 1: Finding Broken Orders or Price Discrepancies
*Scenario: Verifying if any order had rounding errors between the pricing engine and the payment captured.*

```sql
SELECT 
    o.order_id,
    o.customer_email,
    o.calculated_subtotal,
    t.amount_charged_by_gateway,
    (o.calculated_subtotal - t.amount_charged_by_gateway) AS variance_in_cents
FROM orders o
JOIN transactions t ON o.order_id = t.order_id
WHERE o.calculated_subtotal != t.amount_charged_by_gateway;
```
*If this query returns zero rows -> Your pricing engine and payment gateway are in 100% mathematical synchronization!*

---

#### Query 2: Identifying Top Abandoned Custom Blinds Configurations
*Scenario: Helping the business owner understand where customers drop off.*

```sql
SELECT 
    fabric_grade,
    width_fraction,
    COUNT(*) AS total_abandoned_sessions
FROM quote_logs
WHERE converted_to_cart = FALSE
GROUP BY fabric_grade, width_fraction
ORDER BY total_abandoned_sessions DESC
LIMIT 10;
```

---

#### Query 3: Python Data Automation Script (Your In-House Tooling Superpower)
As a Technical BA, you can automate manual Excel reconciliation using a 15-line Python script:

```python
import pandas as pd

# Load website orders and payment gateway transaction export
orders_df = pd.read_csv('janal_orders_october.csv')
gateway_df = pd.read_csv('converge_settlement_october.csv')

# Merge on Invoice Number
reconciliation = pd.merge(orders_df, gateway_df, left_on='invoice_id', right_on='ssl_invoice_number', how='left')

# Find orders marked 'PAID' on site that have no matching bank settlement
missing_payments = reconciliation[reconciliation['settlement_id'].isna()]

if not missing_payments.empty:
    print(f"⚠️ ALERT: Found {len(missing_payments)} un-reconciled orders!")
    missing_payments.to_csv('audit_exceptions.csv', index=False)
else:
    print("✅ 100% Reconciliation Success: All website orders match bank payouts.")
```

---

# Lab 5: User Acceptance Testing (UAT) & Edge-Case Matrix
*Catching Bugs Before Customers Do*

Before any feature goes live, the Technical BA runs the **User Acceptance Testing (UAT)** cycle. You create a structured test matrix with negative and edge cases.

---

### Production UAT Test Matrix (Custom Dimensional eCommerce)

| Test ID | Test Scenario | Steps to Execute | Test Data Used | Expected Result | Pass/Fail | Severity |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **UAT-01** | Minimum Dimension Enforcement | Enter dimensions below 12 inches | Width: `8"`, Height: `20"` | System flags inline warning: "Min width is 12 inches". Add to Cart disabled. | PASS | High |
| **UAT-02** | Fractional Accuracy (1/8") | Select 1/8 fraction and verify math | Width: `40 1/8"`, Height: `60"` | Subtotal displays exactly $197.41 (no floating-point rounding errors). | PASS | Critical |
| **UAT-03** | Expired Credit Card Error Handling | Attempt checkout with expired card | Card: `4000...0002`, Exp: `01/22` | Gateway rejects with friendly error: "Card expired. Please check your date." No crash. | PASS | High |
| **UAT-04** | Form Auto-Save on Refresh | Enter dimensions and refresh page | Width: `72"`, Fabric: `Blackout` | Browser retains selected options in session storage so user doesn't re-type. | PASS | Medium |
| **UAT-05** | Senior Accessibility (WCAG) | Inspect contrast on error messages | Error text `#E11D48` on `#FFFFFF` | Contrast ratio >= 4.5:1, readable for visually impaired users. | PASS | Medium |

---

# Lab 6: Agile Sprint Governance & Defending the Team
*How to Say "No" to Stakeholders with Data & Confidence*

At the $120,000 level, your value is not just in saying "Yes" to everything. Your value is in **protecting the sprint and preventing budget blowouts**.

---

### The Scenario: The Mid-Sprint Emergency
Three days before launch, an executive or client says:  
> *"Hey Benjamin! I was playing with the site this morning and I really want to add an interactive 3D room visualizer so customers can upload photos of their living room before buying blinds. Can we add that to this Friday's release?"*

---

### ❌ The Junior / Bad BA Response
*"Sure, I'll ask the developers to work overtime and see if they can squeeze it in."*  
*(Result: The developers panic, write messy code, introduce 10 critical bugs, miss the launch date, and burn out).*

---

### ✅ The $120,000 Senior Technical BA Response (The "Trade-Off Formula")
You use the **"Acknowledge -> Quantify -> Offer Viable Trade-Offs"** framework:

> *"I love that idea—an interactive room visualizer will be a huge conversion driver for our premium fabric tiers. 
> 
> Here is where our active sprint stands today:  
> Our current release candidate is fully locked. The team has completed QA and security testing on the core dimensional pricing engine and Converge payment gateway. 
> 
> A feature like live photo uploading requires image processing APIs, cloud storage buckets, and mobile camera permissions—that is approximately **40 story points (2 full sprints of engineering effort)**.
> 
> To protect our revenue launch this Friday, I propose two options:
> 
> **Option 1 (Recommended):** We launch Phase 1 on schedule this Friday with the core pricing engine. This starts generating online sales immediately. Meanwhile, I will create the user stories and Figma mockups for the Room Visualizer in our backlog, and we can prioritize it as the marquee feature for **Sprint 18 next month**.
> 
> **Option 2:** If the visualizer is an absolute mandatory prerequisite before taking orders, we can officially adjust our public launch date by 4 weeks to give engineering the necessary runway to build and audit it properly.
> 
> Which direction aligns best with your commercial timeline?"*

**Why this response commands $120,000:**  
You didn't say "no." You treated the client like an adult business partner, gave them full transparency into engineering cost, and gave them control over the trade-off.

---

# 🎯 Your Hands-On Practice Checklist

To internalize these skills before your next interview:
- [ ] **Practice 1:** Open the Jira template in **Lab 2** and read it out loud until you can explain the difference between a user story and acceptance criteria naturally.
- [ ] **Practice 2:** Review the Postman API payload in **Lab 3** and practice explaining the `401 Unauthorized` diagnosis using the words *"origin IP whitelist mismatch."*
- [ ] **Practice 3:** Review the SQL queries in **Lab 4** and understand how you connect database verification to your FreshCo audit background.
- [ ] **Practice 4:** Memorize the 3-step response in **Lab 6** for handling stakeholder scope creep.

You have the skills, the portfolio, and the technical depth. Master these 6 labs, and you will walk into any Technical BA / Product Owner interview with total authority.
