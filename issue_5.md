Production Issue: Wrong Docker Image Deployed
=============================================

Time: Night 9:40 PM
Deployment: user-service latest version deploy chesaru via Jenkins
Impact: Login functionality not working – all users unable to log in
Alert: Uptime alert + client call: "Production login failing"


---

Step-by-Step Investigation

Step 1: Validate Deployment Status
Jenkins job lo build success ayindi
Kani production lo logs check chesina:

kubectl logs user-service-xyz

– Error: "Missing method: validateUserSession()"

Step 2: Docker Image Check

Deployed image:

docker.io/kkfunda/user-service:latest

Checked manually:

docker inspect user-service:latest

→ This tag is pointing to an older dev build, not the new tested release

Step 3: Git Tag vs Image Tag mismatch

Developer code base lo correct version tagged: v2.5.3
But Jenkins pipeline lo tag as latest unchadam valla, old image pull ayyindi.


---

Root Cause (RCA)

Docker image latest tag overwrite ayyindi multiple times

Production pipeline lo fixed version tag (e.g., v2.5.3) vadali, but dynamic latest vadadam valla mismatch ayyindi

No image digest verification chesaru before deployment


Final RCA:
CI/CD pipeline lo improper tagging + No verification → Wrong image deployed → Functionality broken


---

Fix Applied

Immediately rollback chesaru:


kubectl rollout undo deployment user-service

Verified stable image manually:


kubectl set image deployment/user-service user-service=docker.io/kkfunda/user-service:v2.5.2

Updated Jenkinsfile to use versioned tags:


docker build -t kkfunda/user-service:${BUILD_TAG} .


---

Preventive Actions

1. Never use latest tag in production deployments


2. Use versioned, immutable tags (e.g., v2.5.4)


3. Enable image digest lock (SHA-based deployment)


4. Implement image tag validation step before kubectl apply


5. Slack alert or notification for image version deployed to prod




---

KK Funda Voice

> "Docker image wrong version ante small mistake la anipistundi… kani production lo adi pedda crash. DevOps engineer ki versioning, tagging, rollback – anni clear ga undali. Every deployment should be intentional and traceable."
