Production Issue RCA – DevOps Engineer Perspective
==================================================

Issue Summary

Time: 3:45 PM IST

Service: cart-service (Java + Spring Boot)

Environment: Production

Incident: 502 Bad Gateway from ALB

Impact: Users unable to access cart-service; revenue impact



---

Step 1: Incident Detection

Alert received from Datadog for high 5xx errors on cart-service endpoint

Slack channel triggered an alert message automatically

SLA for first response: 15 minutes



---

Step 2: Initial Investigation

Logged into AWS Console to check ALB target group health

Found cart-service instances marked as unhealthy

SSH'd into EC2 instance running the service and confirmed the app wasn't listening on port 8080

Ran systemctl status cart-service and saw that the service had failed to start



---

Step 3: Analyze Logs

Ran journalctl -u cart-service --since "30 minutes ago"

Found error: java.lang.NumberFormatException due to missing environment variable DB_PORT



---

Step 4: Check CI/CD Pipeline

Jenkins pipeline showed a successful deployment

Deployment YAML had a new environment variable (DB_PORT)

Kubernetes Secret didn't include this value, so the container crashed on startup



---

Step 5: Temporary Fix (Hotfix)

Manually updated the missing environment variable in Kubernetes secret using kubectl edit secret

Redeployed the pod using kubectl rollout restart deployment cart-service -n production

App became healthy again and traffic resumed through ALB



---

Step 6: Post-Incident Actions

Issue resolved by 4:20 PM

Total downtime: 35 minutes



---

Root Cause

Jenkins pipeline deployed a change that depended on a new environment variable DB_PORT, but the value was missing in the Kubernetes secret, causing the application to crash and show 502 errors.


---

Corrective Actions

1. Add a secret validation step in the Jenkins pipeline


2. Enable readiness and liveness probes in Kubernetes to detect such issues early


3. Update Helm chart values to ensure all required secrets are included


4. Improve alert descriptions to indicate possible environment variable errors




---

Lessons Learned

Never deploy without validating all required environment variables

Avoid non-backward compatible changes in production

Ensure Dev and DevOps teams are aligned when introducing new config changes



---

Interview Explanation

In one of my previous roles, we faced a 502 error in production due to a missing environment variable. I led the RCA, identified the issue through logs, fixed it by patching the Kubernetes secret, and later added a validation step in the Jenkins pipeline to prevent future occurrences.
