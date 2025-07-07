Production Deployment Failure – Real-Time Scenario with RCA

Situation:
Evening 6:30 PM ki production deployment start chesaru.
Jenkins pipeline successful, but customer portal open avvatledu.
Client nunchi high priority ticket ochindi – “Production site down”.


---

Step-by-Step Investigation:

Step 1: Basic Health Check
DevOps engineer EC2 instance login ayi service status check chesadu.
App service running undi, kani response ledu.

Step 2: Logs Verification
Application logs chusaru –
Error: Database connection refused.

Step 3: Infra Status Check
DB instance status check chesaru →
Aurora DB restarted recently, new endpoint generated.

Step 4: Configuration Mismatch
Latest deployment lo config.yaml file lo DB endpoint update cheyyaledu.
Old endpoint use chesindi, anduke connection fail ayyindi.


---

RCA (Root Cause Analysis):

Root Cause:
DB instance restart valla endpoint change ayyindi.
App configuration lo old endpoint use chesaru.
Code lo environment variable auto pull cheyyadam ledu.

Category:
Configuration management failure.
Lack of validation before production release.

Impact:
Production site down for 14 minutes.
Business users unable to login or place orders.


---

Next Actions / Preventive Steps:

1. Automated Config Check:
CI/CD pipeline lo DB endpoint validation add cheyyadam.


2. Rollback Plan Mandatory:
Deployment ki mundu rollback ready undali (image version or helm rollback).


3. Update Runbooks:
DB endpoint changes epudaithe jaruguthayo, app config automatic update avvadam lekapote manual step mandatory ga mention cheyyali.


4. Slack Alert for DB Restart:
DB instance restart aina alert ops team ki immediate ga ravalani CloudWatch lo rule petti setup cheyyadam.


5. Postmortem Meeting:
Team meeting lo ee incident discuss cheyyadam and ownership clarity ivvadam.


6. Add Pre-deploy Validation Job:
Jenkins pipeline lo config check stage add cheyyadam, especially for DB & critical variables.




---

In Short (Summary):

Issue: Production deployment successful kaani app connect avvatledu
Root Cause: DB endpoint change + config.yaml update avvakapovadam
Fix: Correct endpoint ni add chesi redeploy
Preventive Actions: Config validation, alerts, rollback plan


---

KK Funda Voice:


“Production lo one small config kuda chala pedda issue create chesthundi.
Deployment ante code push kaadu – validation, alerting, backup, and rollback anni plan lo unte ne success.”
