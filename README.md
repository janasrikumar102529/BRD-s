 BUSINESS REQUIREMENTS DOCUMENT (BRD)
 
 Digital Loan Origination & Credit Decisioning
 Unsecured Retail Personal Loans - Digital Channel
 BFSI Business Analysis Portfolio Project

Domain	           - BFSI - Digital Lending (Unsecured Retail Personal Loans)
Document Type	     - Business Requirements Document Version	1.0
Prepared By        - Jana Srikumar - Business Analyst
Purpose	           - To improve the loan application and approval process through a digital, rules-driven origination and decisioning workflow
Regulatory Scope   - RBI Digital Lending Guidelines (Sep 2022); RBI Master Direction - KYC, 2016 (as amended); Fair Practices Code

**1. Executive Summary**
This BRD proposes **a digital loan origination process for unsecured retail personal loans**, where customers can apply online, complete e-KYC, undergo bureau-based credit checks, and receive a rules-driven decision. The goal is to reduce manual work, processing time, and errors while improving customer experience, and to ensure the journey is designed in line with the RBI Digital Lending Guidelines (2022), including borrower consent, a Key Fact Statement (KFS) at sanction, and cooling-off / look-up period disclosures.

**2. Business Problem**
●	Loan applications involve manual data entry and verification.
●	Customers may wait a long time for a decision.
●	Duplicate or incorrect information can create errors.
●	Credit and operations teams spend time on repetitive checks.
●	Customers have limited visibility of their application status.
●	Credit decisions are not always applied consistently across underwriters, creating policy and audit risk.

**3. Business Objectives**
●	Reduce loan processing time (target TAT).
●	Reduce manual verification work through e-KYC and bureau API integration.
●	Improve data accuracy at source through validation.
●	Provide clear application status to customers.
●	Apply credit and eligibility rules consistently through a configurable rules engine.
●	Maintain audit, compliance, and RBI Digital Lending Guideline controls.

**4. Stakeholders**
Stakeholder	Main Role
Customer	Submits and tracks loan application
Sales / Relationship Manager	Helps acquire customers and applications
Credit Team	Reviews credit eligibility; underwrites referred/borderline cases
Operations Team	Verifies documents and processes applications
Risk & Compliance	Checks risk, RBI regulatory requirements, and KFS/consent adherence
Technology Team	Builds and supports the system, including bureau and KYC integrations
5. Current (AS-IS) Process
Customer applies → Documents collected → Manual verification → KYC check → Credit check → Credit decision → Disbursement
Main pain point: too many manual steps can increase processing time and errors.
6. Proposed (TO-BE) Process
Customer applies online → Data validation → e-KYC verification (Aadhaar OTP/biometric) + PAN and CKYC check → Credit bureau check (CIBIL / Experian / CRIF) → Eligibility rules (score, FOIR, income multiple) → Approve / Manual Review / Reject → Key Fact Statement + E-sign → Disbursement.
The system should automatically route simple eligible applications while sending exceptions and borderline cases to the Credit Team for manual review.

**7. Business Requirements**	              
BR-01	Customers should be able to submit a loan application online.
BR-02	The system should validate required customer information.
BR-03	e-KYC (Aadhaar-based OTP/biometric) and PAN verification should be completed before final approval, with a CKYC registry check to avoid duplicate KYC.
BR-04	The system should perform the required credit bureau check (CIBIL / Experian / CRIF).
BR-05	The system should evaluate the application using approved eligibility rules (credit score, FOIR, income multiple, age, employment vintage).
BR-06	Applications should be routed to approval, manual review, or rejection.
BR-07	Customers should be able to track application status.
BR-08	Important application actions should be recorded for audit purposes, retained per RBI record-retention norms.
BR-09	The customer must receive a Key Fact Statement (KFS) at sanction and provide explicit consent before disbursement, per RBI Digital Lending Guidelines.

**8. Key Business Rules**
Rule 1	Mandatory fields (name, PAN, Aadhaar reference, income, employment details) must be completed before submission.
Rule 2	e-KYC must be successfully verified (Aadhaar OTP/biometric + PAN validation) before final approval; re-KYC applies per RBI periodicity norms.
Rule 3	Minimum bureau credit score of 700 (CIBIL scale) is required for straight-through auto-approval.
Rule 4	Fixed Obligation to Income Ratio (FOIR) must not exceed 50% of net monthly income.
Rule 5	Maximum eligible loan amount is capped at 10x average net monthly income, subject to the product's maximum ticket size for unsecured personal loans.
Rule 6	Applicant age must be between 21 and 58 years at loan maturity; minimum employment vintage of 1 year (salaried) or 2 years (self-employed).
Rule 7	Applications with a bureau score between 650–699, or FOIR between 50–55%, are routed to the Credit Team for manual review.
Rule 8	Applications with a bureau score below 650, failed KYC, or failing mandatory policy rules are auto-rejected.
Rule 9	Duplicate applications (same PAN or Aadhaar reference within 30 days) should be flagged for review.

**9. Functional Requirements**
FR-01	System shall allow customers to enter personal, employment, income, and loan details.
FR-02	System shall show an error when mandatory information is missing.
FR-03	System shall initiate e-KYC verification via Aadhaar OTP/biometric and validate PAN via NSDL/Income Tax e-filing API.
FR-04	System shall check the CKYC registry before initiating fresh KYC, to reuse existing verified KYC records where valid.
FR-05	System shall call the credit bureau API (CIBIL / Experian / CRIF) and capture the score and bureau response code.
FR-06	System shall evaluate configured eligibility rules (score, FOIR, income multiple, age, vintage) via a rules engine.
FR-07	System shall update application status and make it visible to the customer in real time.
FR-08	System shall generate a Key Fact Statement (KFS) at sanction and capture explicit borrower consent before disbursement.
FR-09	System shall send the application to the correct workflow (auto-approve, manual review queue, or rejection) based on the decision.

**10. Non-Functional Requirements**
●	Security    : Customer PII and financial data encrypted in transit (TLS 1.2+) and at rest (AES-256); bureau and Aadhaar data masked in application logs.
●	Performance : Page transitions should complete within 3 seconds; credit bureau API calls should respond within 5 seconds, with a retry/fallback path on timeout.
●	Availability: The application should maintain at least 99.5% uptime during business hours (8 AM–10 PM IST).
●	Audit       : All decisions, status changes, and manual overrides should be logged with timestamp and user ID, retained per RBI record-retention requirements (minimum 8 years).
●	Scalability : The system should support at least 3x current peak application volume during campaign or festive periods.

**11. Data Requirements**
Customer	-Name, date of birth, contact details, address, PAN, masked Aadhaar reference number
Employment-Employer, employment type, income, employment vintage
Loan    	-Loan type, amount, tenure, application status
KYC     	-e-KYC status, CKYC number, KYC verification date, re-KYC due date
Credit	  -Bureau score, bureau response code, FOIR, existing obligations

**12. Exception Scenarios**
e-KYC fails                   	      -Place application on hold and request further verification (e.g., alternate KYC mode).
Credit bureau service unavailable     -Keep application pending and retry per defined retry policy; if unresolved, route to manual underwriting.
Bureau score unavailable / thin file  -Route to Credit Team for manual underwriting using alternate data where policy permits.
Duplicate application              	  -Flag the application for review.
Borderline eligibility	              -Send application to Credit Team for manual review.
Customer does not accept KFS / consent-Do not proceed to disbursement; close or hold the application per policy.

**13. Key KPIs**
Loan Processing TAT	                  -Measures how quickly applications are processed end-to-end.
Approval Rate                         -Shows the percentage of applications approved.
Application Drop-off Rate             -Shows where customers leave the process.
Manual Review Rate	                  -Shows how much work still needs manual handling.
Straight-Through Processing (STP) Rate-Measures successful fully-automated processing without manual intervention.
Bureau Hit Rate                       -Share of applications with a usable bureau score, indicating data/onboarding quality.
Cost per Application                  -Helps measure operational efficiency.

**14. Risks and Mitigation**
Incorrect customer data	      -Use validation rules at entry.
Service downtime (KYC/bureau)	-Use retry and exception handling with manual fallback.
Incorrect business rules	    -Review rules with Credit and Risk teams and test them before go-live.
Unauthorized access	          -Use role-based access and audit logs.
Non-compliance with RBI Digital Lending Guidelines -	Involve Compliance in design review; ensure KFS, consent, and disclosure requirements are built in before launch.

**15. Assumptions & Dependencies**
Assumptions
●	Required customer data can be collected digitally.
●	Business eligibility rules (score, FOIR, income multiple thresholds) will be provided and periodically reviewed by Credit/Risk teams.
Dependencies
●	e-KYC / CKYC service
●	Credit bureau service (CIBIL / Experian / CRIF)
●	Loan management system
●	Notification service
●	E-signature service

**16. Acceptance Criteria – Example**
Given a customer submits a complete loan application, when required e-KYC and credit bureau checks are completed, then the system should evaluate the application against approved business rules (score ≥ 700 and FOIR ≤ 50% for auto-approval) and route it to Approval, Manual Review, or Rejection, and generate a Key Fact Statement before disbursement.

**17. Regulatory & Compliance References**
●	RBI Digital Lending Guidelines, 2022 - governs borrower consent, Key Fact Statement (KFS), and disclosure norms for digital loans.
●	RBI Master Direction - Know Your Customer (KYC), 2016 (as amended) - governs e-KYC, CKYC, and periodic re-KYC requirements.
●	Fair Practices Code - governs transparent communication of terms, charges, and grievance redressal to the borrower.

**18. Expected Business Benefits**
●	Faster loan processing.
●	Less manual operational work.
●	Better customer experience.
●	More consistent credit decisions.
●	Improved data quality and auditability.
●	Stronger regulatory compliance posture with RBI Digital Lending Guidelines.
