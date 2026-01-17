# AXIS-AML-Compliance-Engine
AXIS | AML & Compliance Engine
Status: Production (Private)

Context
AXIS is a specialized backend system I built to handle Anti-Money Laundering (AML) checks for high-volume financial transactions. It acts as the "gatekeeper" between the payment processor and the banking ledger.

⚠️ Note on Source Code: Because this system processes sensitive financial data and contains proprietary risk algorithms, the source code is not public. This repository serves as documentation of the architecture, the problems I solved, and the UI workflow.

What it actually handles
The system automates the compliance legwork that usually slows down fintech products:

Sanction Screening: Before a user can transact, the system runs them against global watchlists (OFAC, EU, UN) to ensure they aren't blacklisted.

Transaction Monitoring: It analyzes transaction patterns in real-time. If a user tries to structure payments (e.g., sending 49k three times to avoid a 150k limit), AXIS flags it.

Risk Scoring: Assigns a dynamic risk score to users. High-risk users get manual review; low-risk users get automated approvals.

The Tech Stack
Core Backend: Python (Django)

Database: PostgreSQL (Strict relational schemas for audit trails)

Async Tasks: Celery + Redis (To handle blocking tasks like generating PDF reports without slowing down the API)

Frontend: React (For the Compliance Officer Dashboard)

Architecture Flow
Since I can't show the code, here is how the data flows "under the hood":

Request: External service sends a transaction payload via API.

Validation: AXIS validates the schema and checks the user's current status.

The Logic Layer:

Checks velocity limits (How many transactions in the last hour?).

Checks value limits (Is this above their tier?).

Checks destination (Is this a high-risk country?).

Verdict: The system returns a PASS, BLOCK, or REVIEW status instantly.

Dashboard Preview
(This is the internal tool compliance officers use to view flagged/held transactions)

Real-time feed of incoming transactions and their risk status.

Detailed view where officers can approve or reject a flagged transfer.

Challenges I Solved
Latency: The biggest challenge was keeping the response time under 200ms while running multiple database checks. I solved this by caching user profiles in Redis so we didn't hit the main DB for every single check.

False Positives: Tweaking the string-matching algorithm so legitimate users didn't get blocked just because they shared a name with someone on a watchlist.
