Production Issue: Home Page Loading Slow for All Users
=======================================================

Time: Morning 10:20 AM
Alert: New Relic alert – "Homepage API latency > 3s for 95% users"
Impact: Customers are complaining app slow undi ani
Service: Frontend Service (React + Node.js)


---

Step-by-Step Investigation

Step 1: Monitoring tools open chesaru (New Relic, Grafana)
– Request count stable undi
– Latency sudden ga perigindi
– DB queries execution time spike chesindi

Step 2: DB logs check chesaru
Aurora logs lo recent query time 7+ seconds ki perigindi
Query: SELECT * FROM products WHERE status = 'available';

Step 3: Query Execution Plan chusaru
Index use avvatam ledu → full table scan jarugutondi


---

Root Cause (RCA)

Last night developer code push chesadu with a small change

New column product_visibility add chesadu, but index create cheyyaledu

Existing query now fetching based on new column too

DB slow response valla app frontend loading delay avutondi


Final RCA:
Missing DB index after schema change
Leads to full table scan and high latency


---

Fix Applied

Infra team immediate ga DB index create chesaru on product_visibility column

Query optimized and execution time dropped to < 200ms

App response speed back to normal



---

Preventive Actions

1. DB Index Review Process:
Any schema changes should go through index requirement validation.


2. Performance Testing Before Prod Deploy:
UAT lo query performance check mandatory.


3. Query Monitoring Setup:
Long running query alerts enable cheyyadam


4. Developer Checklist Update:
Code push ki mundu DB optimization verify cheyyadam




---

KK Funda Voice

> "Production issues anni pedda bugs tho raanu. Sometimes oka index lekapotam valla app loading slow ayipothundi. DevOps engineer ki developer code change impact kuda artham avutundali. Tools use cheyyadam oka side, but problem ki root ki velle thinking important."
