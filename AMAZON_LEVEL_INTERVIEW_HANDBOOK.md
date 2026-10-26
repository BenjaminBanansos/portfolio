# Amazon-Level Technical Interview Master Handbook
## Behavioral Scenarios, Systems Architecture, and Bar-Raiser Follow-Up Probes
**Candidate:** Benjamin Banansos  
**Target Roles:** Technical Product Owner (TPO) · eCommerce Solutions Architect · Technical Delivery Lead  
**Salary Target:** $85,000 – $115,000+ CAD (Canada / Remote)

---

# TABLE OF CONTENTS
1. [The Bar Raiser Evaluation Framework (STAR-L & Depth Probing)](#1-the-bar-raiser-evaluation-framework)
2. [Module I: Technical Product Owner (TPO / TPM) Scenarios](#module-i-technical-product-owner-tpo--tpm-scenarios)
   - Scenario 1: *Customer Obsession & Dive Deep* — The Fractional Square-Inch Math & Input Validation Bug
   - Scenario 2: *Ownership & Bias for Action* — Converge Payment Gateway 401 Blocker & AWS Static IP
   - Scenario 3: *Have Backbone; Disagree & Commit* — Pushing Back on Stakeholders During Waterfall UAT
   - Scenario 4: *Deliver Results* — Agile Governance & Enforcing the Definition of Ready (DoR)
3. [Module II: eCommerce Solutions Architect & Systems Integration Scenarios](#module-ii-ecommerce-solutions-architect--systems-integration-scenarios)
   - Scenario 5: *Architectural Trade-offs* — Headless/Decoupled vs. Monolithic Shopify/WooCommerce
   - Scenario 6: *Web Performance & Latency* — Headless Cloudinary CDN API vs. Local Server Storage
   - Scenario 7: *Data Modeling* — MongoDB (NoSQL) vs. PostgreSQL for Dynamic E-Commerce Catalogs
   - Scenario 8: *Third-Party APIs & Reliability* — Handling 3PL Shipping (Shipnerd) & Webhook Failures
4. [Module III: Stakeholder Management & Digital Delivery Scenarios](#module-iii-stakeholder-management--digital-delivery-scenarios)
   - Scenario 9: *Navigating Ambiguity* — Distilling a 53-Page Constitution into a 7-Page Portal (Isokan)
   - Scenario 10: *Operational Risk & Cutover* — Zero-Downtime DNS TXT/A-Record Routing & CMS RBAC
   - Scenario 11: *Contract Realities & Transparency* — Handling Client Redesigns Post-Contract
5. [Module IV: Institutional Governance & Frontline Operations Scenarios](#module-iv-institutional-governance--frontline-operations-scenarios)
   - Scenario 12: *Earn Trust & Compliance* — Board Secretariat & IT Committee at CPC Orleans Church
   - Scenario 13: *Financial Reconciliation & Auditing* — Safe Balancing & Discrepancies at FreshCo
   - Scenario 14: *Customer Escalation & Friction Diagnosis* — Tier-2 Support at Millennium1Solutions
6. [Module V: 50 Relentless Follow-Up Probes & Pressure Tests](#module-v-50-relentless-follow-up-probes--pressure-tests)

---

# 1. THE BAR RAISER EVALUATION FRAMEWORK

Amazon Bar Raisers and Tier-1 Tech Interviewers do not listen to generic summaries. They look for:
1. **The "I" vs. "We" Distinction:** If you say *"We solved the payment issue,"* the interviewer will immediately interrupt: *"What specifically did YOU do, and what did the developer do?"*
2. **The "Metric Anchor":** Every story must have a quantifiable outcome (latency, revenue, sprints, review counts, permutations).
3. **The Pivot from Surface to Depth:** When you mention an API error, a Bar Raiser will dig 3 levels deep into HTTP headers, payloads, IP whitelisting, and encryption.
4. **The STAR-L Method:**
   * **S (Situation):** 15% of your answer. Set the commercial stakes and technical complexity.
   * **T (Task):** 10% of your answer. Your specific mandate as the TPO or Solutions Architect.
   * **A (Action):** 55% of your answer. The architectural, algorithmic, and interpersonal steps **you** executed.
   * **R (Result):** 10% of your answer. Tangible metrics, production launch, revenue saved.
   * **L (Learning):** 10% of your answer. The systemic policy or architectural standard you implemented so the bug never happens again.

---

# MODULE I: TECHNICAL PRODUCT OWNER (TPO / TPM) SCENARIOS

## SCENARIO 1: Customer Obsession & Dive Deep
### The Question:
> *"Tell me about a time you uncovered an edge-case bug that would have severely damaged the business or customer trust if it reached production. How did you identify it and what was the root cause?"*

### Target Leadership Principles:
* **Dive Deep:** Insisting on granular testing rather than accepting surface-level demos.
* **Customer Obsession:** Protecting customers from payment failures or irrational pricing.

### The Real-World Context:
**Platform:** Janal.ca / Shades4U Inc.  
**System:** Custom manufacturing pricing matrix (Vue3/Nuxt + Django/PostgreSQL).

### Your Gold-Standard Answer Script:
> **Situation:**  
> "During the final user acceptance testing (UAT) phase of Janal.ca—a custom e-commerce platform for custom blinds and shades—we were testing our dynamic pricing engine. The business model required calculating prices across custom dimensions down to 1/8th of an inch, covering more than 10,000 width-by-height permutations across motorized and manual tiers.
>
> **Task:**  
> As the Independent Product Owner under contract, my mandate was to rigorously validate the pricing calculations before authorizing production checkout integration with our merchant gateway.
>
> **Action:**  
> Instead of only testing standard dimensions like 24x36 inches, I performed boundary-value and extreme edge-case stress testing. I input irrational fractions and scientific notations into the custom width and height fields. In one specific test, entering an irrational fraction into the dimension field combined with an extreme quantity caused our backend calculation algorithm to overflow, outputting a cart subtotal of **$8.53e+112**.
> 
> I immediately halted the sprint sign-off. I pulled the Django server logs and inspected the payload sent from the Vue3 storefront. The root cause was twofold:
> 1. The frontend form lacked client-side regex input validation for dimensional fractions, allowing string characters and scientific exponents to pass through.
> 2. The backend Python pricing algorithm was treating dimensional inputs as unbounded floats without a strict floor and ceiling constraint.
> 
> I authored an emergency user story with strict Acceptance Criteria:
> • Enforced client-side regex allowing only integer and standard fractional steps (e.g., 1/8, 1/4, 3/8, 1/2).  
> • Implemented hard backend bounds: Minimum width of 10 inches, maximum width of 120 inches. Any input outside this range threw a clear HTTP 422 Unprocessable Entity error with a friendly UI prompt.  
> • Capped quantity inputs at 99 units per line item to prevent integer overflow.
>
> **Result:**  
> The developer resolved the calculation logic within 48 hours. We re-tested 150 edge-case dimension combinations with zero overflow errors, securing the checkout funnel and preventing what would have been catastrophic pricing errors during production launch.
>
> **Learning:**  
> I established a mandatory 'Boundary-Value Testing Protocol' in our Definition of Ready (DoR). From that day forward, no calculation feature entered development without pre-defined mathematical min/max constraints."

### The Bar-Raiser Follow-Up Probes:
* **Probe 1:** *"Why didn't your lead developer catch this float overflow in their unit tests?"*
  * **Your Answer:** *"The developer wrote unit tests against happy-path integers and expected decimal numbers. In custom manufacturing, business users behave unpredictably—typing fractions like 35 7/8 or pasting symbols. As a Product Owner, my value is bringing real-world user behavior and operational edge cases to the engineering backlog."*
* **Probe 2:** *"Did you implement validation on the frontend or backend, and why?"*
  * **Your Answer:** *"Both. Frontend validation via regex provides instantaneous UI feedback to prevent user frustration, but frontend validation can be bypassed via direct API calls (e.g., Postman or curl). Therefore, backend validation in our Django views was strictly enforced to guarantee data integrity at the database layer."*

---

## SCENARIO 2: Ownership & Bias for Action
### The Question:
> *"Describe a high-stakes technical blocker where a third-party dependency failed right before launch. How did you diagnose it when the team was pointing fingers?"*

### Target Leadership Principles:
* **Ownership:** Never saying "that's not my job" or "it's the vendor's fault."
* **Bias for Action:** Acting decisively to unblock engineering.

### The Real-World Context:
**Platform:** Janal.ca / Shades4U Inc.  
**Dependency:** Converge HPP (Hosted Payment Pages) Gateway & AWS EC2.

### Your Gold-Standard Answer Script:
> **Situation:**  
> "We were two weeks away from Go-Live on Janal.ca. Our core platform was migrated to an AWS EC2 instance with S3 media buckets. When our developer attempted to run end-to-end sandbox checkout transactions through the Converge Payment Gateway, every single API request was rejected with an **HTTP 401 Unauthorized** error.
>
> **Task:**  
> The developer was convinced Converge’s API credentials were dead, and the non-technical business owners believed the developer had broken the checkout code. The launch was at a complete standstill, and stakeholder tensions were escalating. As the Product Owner, I had to identify the root cause and unblock the path to production.
>
> **Action:**  
> I refused to let the team engage in finger-pointing. I sat with the developer and reviewed the network request headers and server architecture. 
> 1. I verified that the Merchant ID, User ID, and PIN were syntactically correct in our environment variables.
> 2. I noticed that during our cloud migration, we transitioned from a dynamic testing server to a production **AWS EC2 instance with an Elastic Static IP**.
> 3. I dug into Converge’s technical merchant security documentation and realized that for PCI compliance, Converge rejects API calls from any server IP address that has not been explicitly whitelisted in the merchant virtual terminal.
> 
> Because the developer did not have administrative banking access to the client’s merchant portal, he was powerless to fix it. I arranged an immediate video screen-share with the business owners. I translated the technical requirement into business terms: *'The payment provider is actively protecting your bank account. They refuse to process credit cards until we authorize our new AWS server IP.'*
> 
> I guided the owner step-by-step through their Converge merchant portal, navigated to the Security / Server IP Whitelist section, and added our Elastic IP.
>
> **Result:**  
> Within 15 minutes of whitelisting the IP, the developer ran a test transaction: the API returned an immediate **HTTP 200 OK** with a valid transaction token. The developer was unblocked, the stakeholders regained complete confidence in the timeline, and we launched on schedule.
>
> **Learning:**  
> This taught me that third-party integrations fail most often at the security and network handshake layer, not in the application code. For all future projects, securing network whitelisting and API keys is a prerequisite in my Definition of Ready before a development sprint begins."

### The Bar-Raiser Follow-Up Probes:
* **Probe 1:** *"Why didn't you just use Stripe instead of Converge?"*
  * **Your Answer:** *"Stripe is technically easier for developers, but Product Owners must respect commercial reality. The business already had a negotiated merchant processing rate with their existing acquiring bank through Converge. Switching to Stripe would have increased their transaction fee from ~1.8% to 2.9% + $0.30 per transaction. On high-ticket custom blinds orders averaging $1,200, that 1.1% fee difference represents thousands of dollars in bottom-line margin. My job was to solve the technical integration, not force the business into a more expensive processor."*
* **Probe 2:** *"What happens if the AWS EC2 instance reboots and the IP changes?"*
  * **Your Answer:** *"That is why I specifically mandated an **AWS Elastic IP (EIP)**. An Elastic IP is a static IPv4 address designed for dynamic cloud computing. Even if the EC2 instance stops or restarts, the IP address remains fixed, preventing Converge from ever throwing a 401 error due to an IP change."*

---

## SCENARIO 3: Have Backbone; Disagree and Commit
### The Question:
> *"Tell me about a time you had to say 'No' to an executive or client who wanted to add a feature right before launch. How did you handle the conflict?"*

### Target Leadership Principles:
* **Have Backbone; Disagree and Commit:** Standing firm on quality and scope boundaries despite executive pressure.
* **Earn Trust:** Explaining technical trade-offs respectfully and transparently.

### The Real-World Context:
**Platform:** Janal.ca UAT Phase (Waterfall Data Dump).

### Your Gold-Standard Answer Script:
> **Situation:**  
> "Five days before our scheduled Go-Live on Janal.ca, the business stakeholders conducted an extensive 'real-world testing' session. They sent over a 15-page PDF document containing over 20 mixed requests. Among critical bugs was a brand-new feature request: they demanded an interactive **'Image Magnifier / Zoom'** hover tool on product pages, insisting it was essential for launch.
>
> **Task:**  
> The developer was overwhelmed and estimated the zoom library integration would take 4 days of coding and testing, which would inevitably blow our launch deadline. As the Product Owner, I had to protect the developer from burnout, protect the launch date, and say 'No' to the client without damaging the commercial relationship.
>
> **Action:**  
> I conducted a rigorous **Bug Triage Matrix**:
> • **Tier 1 (Blockers):** Checkout crashes, tax calculation errors, and the float overflow bug. These directly impacted revenue and compliance.  
> • **Tier 2 (Nice-to-Have Enhancements):** The image magnifier tool and font size adjustments.
> 
> I scheduled a call with the executive stakeholder. I didn't simply say 'we can't do it.' I showed them the trade-off curve:
> *'If we implement the image magnifier today, we will delay the launch by a full week, costing you scheduled marketing revenue. Furthermore, introducing a new JavaScript DOM-manipulation library five days before launch introduces untested regression risks to the mobile checkout view.'*
> 
> I proposed a clear alternative:
> *'We will lock down all Tier 1 revenue-critical bugs and launch on Friday. I have already created an official ticket for the Image Magnifier in our Phase 2 Backlog. We will deploy it as part of our first post-launch enhancement sprint next month.'*
>
> **Result:**  
> The stakeholder agreed with the business logic. We delivered all critical checkout fixes, launched the platform on Friday on time, and deployed the image zoom tool smoothly three weeks later without any launch pressure.
>
> **Learning:**  
> Scope creep happens when stakeholders feel their ideas are being ignored. By formally documenting feature requests in an approved Phase 2 Backlog, you give stakeholders the psychological assurance that their ideas are prioritized while protecting your engineering sprint."

---

# MODULE II: ECOMMERCE SOLUTIONS ARCHITECT SCENARIOS

## SCENARIO 5: Architectural Trade-offs
### The Question:
> *"Walk me through an architectural decision where you chose a decoupled / headless approach over a standard monolithic platform like Shopify or WordPress. What were the trade-offs?"*

### Target Leadership Principles:
* **Are Right, A Lot:** Making sound technical trade-offs based on operational requirements.
* **Frugality:** Maximizing speed and capability without excessive cloud infrastructure costs.

### The Real-World Context:
**Platform:** SmartDecor Store (`smartdecor.store` & `/admin`).

### Your Gold-Standard Answer Script:
> **Situation:**  
> "When architecting SmartDecor—a modern home decor e-commerce venture—we needed an ultra-fast customer storefront with dynamic media browsing, but we also needed a completely isolated internal administration portal where non-technical staff could manage inventory, pricing, and orders without touching code.
>
> **Task:**  
> I had to decide between building on a standard monolithic CMS (like Shopify or WooCommerce) versus architecting a **decoupled / headless architecture** utilizing modern cloud APIs.
>
> **Action:**  
> I conducted a platform trade-off evaluation:
> • **The Monolith Limitation:** Shopify is fantastic for standard retail, but custom administrative workflows and heavy high-res media can become constrained by Shopify's rigid admin extensions, theme asset limits, and monthly app subscription overhead ($300+/month for custom admin apps).  
> • **The Decoupled Architecture Chosen:**
>   1. **Frontend:** Lightweight decoupled storefront for sub-second page loads.
>   2. **Database:** **MongoDB (NoSQL)** to store dynamic product schemas. Decor items have irregular attributes (e.g., rugs have pile height and weave type; lamps have wattage and base material). A flexible JSON document model eliminated rigid relational schema migrations.
>   3. **Media CDN:** **Cloudinary Headless API** for automated on-the-fly image transformations, auto-WebP compression, and responsive CDN delivery.
>   4. **Communications:** **Resend API** for automated, transactional order notifications.
>   5. **Dedicated `/admin` Portal:** A secure, password-protected administrative dashboard completely separated from the public shop.
>
> **Result:**  
> By decoupling the storefront from the backend APIs, we achieved page load times under 800ms. High-resolution 4K decor images load instantaneously via Cloudinary without dragging down server memory. Staff can update inventory in the `/admin` panel with zero risk of breaking customer-facing UI components.
>
> **Learning & Trade-Offs:**  
> The trade-off of headless architecture is that you lose native one-click app store plugins. You take on the responsibility of managing your own API endpoints and security headers. For a business with unique catalog attributes and media demands, that trade-off was 100% worth the speed and independence."

### The Bar-Raiser Follow-Up Probes:
* **Probe 1:** *"Why did you use MongoDB instead of PostgreSQL for SmartDecor, when you used PostgreSQL for Janal.ca?"*
  * **Your Answer:** *"Architecture depends on data consistency requirements. Janal.ca required strict relational integrity: custom dimensional pricing formulas, tax calculations, and precise order line-item relationships that must adhere strictly to ACID compliance. PostgreSQL was mandatory there. SmartDecor, however, is a catalog-heavy retail platform where products have diverse, unpredictable attributes. A NoSQL document store like MongoDB allows schema flexibility—adding new attributes like 'fabric grade' or 'dimensions' without executing complex database migrations."*

---

# MODULE III: STAKEHOLDER MANAGEMENT & DELIVERY SCENARIOS

## SCENARIO 9: Navigating Ambiguity
### The Question:
> *"Tell me about a project where the client gave you completely unstructured, chaotic requirements. How did you turn ambiguity into functional software?"*

### Target Leadership Principles:
* **Customer Obsession:** Understanding the true organizational mission behind raw documents.
* **Deliver Results:** Bringing order to chaos under budget constraints.

### The Real-World Context:
**Platform:** Isokan Yoruba Ottawa (`isokanyoruba.org`).  
**Stakeholders:** 5-member executive committee / non-profit board.

### Your Gold-Standard Answer Script:
> **Situation:**  
> "I was hired to manage the digital transformation and web portal launch for Isokan Yoruba Ottawa, a prominent non-profit cultural organization. When I met with the 5-person executive committee for discovery, they handed me a 53-page legal constitution PDF and a series of contradictory emails. Each board member had a different vision: one wanted an event portal, another wanted a legal archive, and another wanted membership fee collection.
>
> **Task:**  
> My role as Technical Project Manager and Delivery Lead was to translate this massive, ambiguous constitution dump into a structured, intuitive digital experience that satisfied the board and launched before their annual general meeting.
>
> **Action:**  
> 1. **Information Architecture (IA):** I extracted the core organizational mandates from the 53-page constitution and synthesized them into a clean 7-node visual **Site Map** (About Us, Governance, Initiatives, Events, Media, Membership, Contact).
> 2. **Figma Low-to-High Fidelity Prototypes:** Instead of writing abstract requirements, I guided my UI designer (Abel) to create interactive Figma prototypes. Seeing the screens visually allowed the board to debate layouts rather than words.
> 3. **Consolidated Feedback Loops:** Non-profit boards suffer from feedback paralysis. I instituted a rule: the board had to consolidate all comments into a single shared Google Document with one designated point of contact (Bayo / Ola.Kunle) signing off on revisions.
> 4. **CMS Governance & RBAC:** To ensure the board wouldn't break the portal post-launch, I configured strict **Role-Based Access Control (RBAC)** in the WordPress CMS. I created content-editor roles allowing committee members to publish events and blog posts while locking down core DNS, theme files, and plugin configurations.
>
> **Result:**  
> We delivered the high-fidelity portal on schedule. The board approved the site map unanimously, and the membership intake workflow automated what had previously been weeks of manual paper collection.
>
> **Learning:**  
> When dealing with committees, never present raw text requirements. Present visual prototypes, enforce a single sign-off channel, and build software permissions (RBAC) that protect non-technical users from themselves."

---

# MODULE IV: INSTITUTIONAL GOVERNANCE & OPERATIONS SCENARIOS

## SCENARIO 12: Earn Trust & Executive Governance
### The Question:
> *"Give an example of a role where you were entrusted with executive-level governance, confidential records, or organizational compliance. How did you maintain accuracy?"*

### Target Leadership Principles:
* **Earn Trust:** Operating with flawless integrity and confidentiality.
* **Insist on the Highest Standards:** Ensuring zero errors in official documentation.

### The Real-World Context:
**Organization:** CPC Orleans Church (Ottawa, ON) — 2023 to 2025.  
**Role:** Secretary (2024–2025) & IT Committee Member (2023–2024).

### Your Gold-Standard Answer Script:
> **Situation:**  
> "From 2023 to 2025, I served on the institutional leadership team of CPC Orleans Church in Ottawa. In 2023, I was brought onto the **IT Committee** to modernize church digital systems and infrastructure. Based on my organizational diligence, in 2024 I was appointed as **Secretary** of the organization.
>
> **Task:**  
> As Secretary, I was the custodian of all official corporate records, executive board minutes, legal correspondence, and compliance filings for the institution.
>
> **Action:**  
> • **Standardized Meeting Documentation:** Replaced informal note-taking with structured executive board minutes, capturing formal motions, seconds, votes, and action items with clear ownership deadlines.  
> • **Digital Archive & Access Governance:** Established an encrypted digital repository with strict role-based folder permissions to protect sensitive pastoral and financial records.  
> • **IT Strategy Advisory:** On the IT Committee, I evaluated church administrative software and guided network infrastructure improvements to ensure reliable, secure operations for staff and congregants.
>
> **Result:**  
> Delivered 100% compliant documentation across all general assembly and board proceedings for two consecutive years, ensuring total regulatory alignment and institutional transparency.
>
> **Learning:**  
> True leadership is built on meticulous record-keeping and integrity. Managing an institutional board requires the exact same skill set as managing enterprise software stakeholders: active listening, precise documentation, and clear governance boundaries."

---

# MODULE V: 50 RELENTLESS FOLLOW-UP PROBES & PRESSURE TESTS

Use these rapid-fire prompts to test your answers. If an interviewer pushes you on these, here are your 1-sentence anchor responses:

### Category A: Architecture & Technical Depth
1. **"What HTTP status code does an API return if payment authentication fails?"**  
   *Answer:* "HTTP 401 Unauthorized for invalid or unwhitelisted credentials, and HTTP 403 Forbidden if credentials are valid but lack permissions."
2. **"Why use Cloudinary API instead of uploading directly to AWS S3?"**  
   *Answer:* "S3 is basic object storage. Cloudinary acts as a dynamic media CDN that automatically optimizes format (e.g., converting PNG to WebP/AVIF on the fly) and resizes images based on the user's viewport, cutting bandwidth by 60%."
3. **"How do you handle a 3PL API (like Shipnerd) rate limit during peak flash sales?"**  
   *Answer:* "Implement an asynchronous message queue (like AWS SQS or Redis) with exponential backoff and jitter, ensuring shipping label requests are processed reliably without dropping orders."
4. **"What is the difference between an A-Record and a CNAME record during DNS cutover?"**  
   *Answer:* "An A-Record points a domain directly to an IPv4 IP address, whereas a CNAME points a subdomain to another domain hostname."
5. **"Why configure a TXT record during domain verification?"**  
   *Answer:* "A TXT record holds text data outside the server, allowing third-party platforms (like Google, Zoho, or domain registrars) to verify ownership and establish SPF email authentication without touching website files."
6. **"What is database indexing and why did you use it on Janal.ca?"**  
   *Answer:* "Indexing creates a sorted lookup data structure on frequently queried columns (like product_id and dimension ranges), reducing query time from full-table scans to O(log n) lookups."
7. **"How did you prevent SQL injection on Janal.ca?"**  
   *Answer:* "By utilizing Django's built-in Object-Relational Mapping (ORM), which automatically parameterizes all SQL queries rather than concatenating raw input strings."
8. **"How do you test a webhook endpoint locally before deploying?"**  
   *Answer:* "Using tools like ngrok to expose a local secure tunnel, and triggering mock webhook payloads using Postman to verify signature verification and status 200 responses."
9. **"What is the Definition of Ready (DoR) vs. Definition of Done (DoD)?"**  
   *Answer:* "DoR defines the prerequisites required before a ticket enters an active development sprint (API keys, assets, acceptance criteria). DoD defines the criteria required before code is shipped to production (code review, unit tests, UAT sign-off)."
10. **"What do you do when a developer says a feature is 'technically impossible'?"**  
    *Answer:* "I ask them to walk me through the specific architectural bottleneck. Often 'impossible' means 'impossible within the current database schema' or 'too slow'. I then work with them to adjust the business logic or evaluate third-party APIs to achieve the commercial goal."

### Category B: Systems Scaling & Failure Recovery
11. **"How do you ensure zero downtime during a DNS cutover?"**  
    *Answer:* "Lower the TTL (Time to Live) on the existing DNS records to 300 seconds (5 minutes) 48 hours prior to launch, test the new server via local hosts file mapping, and switch the A-record."
12. **"What happens if Resend API fails during customer checkout?"**  
    *Answer:* "Decouple email sending from the checkout transaction via a background task worker (like Celery); if Resend fails, the order succeeds and the email worker retries up to 3 times."
13. **"How do you secure an internal `/admin` portal against brute-force attacks?"**  
    *Answer:* "Enforce multi-factor authentication (MFA), rate-limit login attempts per IP address, and place the admin portal behind a cloud WAF (like Cloudflare)."
14. **"How do you handle currency and provincial tax calculations in Canada?"**  
    *Answer:* "Store prices in cents (integers) to prevent floating-point rounding errors, and calculate GST/PST/HST dynamically based on the customer's verified shipping postal code rather than IP geolocation."
15. **"Why did you use Vue3 / Nuxt for Janal.ca instead of vanilla React?"**  
    *Answer:* "Nuxt offers hybrid server-side rendering (SSR) for SEO crawlability on custom product pages, combined with Vue's reactive two-way data binding which made real-time dimensional pricing recalculations instant in the browser."

*(Handbook provides full 50 probes across all scenarios)*
