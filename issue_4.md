Production Issue: Disk Full Due to Logs
========================================

Time: Afternoon 3:15 PM
Alert: EC2 instance /var disk 95% usage reach ayyindi
Service: payment-service running on that server
Impact: Jenkins job build failure + application logs stop avvadam start ayyindi


---

Step-by-Step Investigation

Step 1: Disk usage check chesaru

df -h

– /var/log/ almost 100% full

Step 2: Check which files are large

du -sh /var/log/*

– Found payment-service.log file is 6GB+

Step 3: Check log rotation status

ls /etc/logrotate.d/

– For this service, no rotation config found. So logs are continuously growing


---

Root Cause (RCA)

payment-service logs continuous ga /var/log/ lo generate avutunnayi

logrotate configure cheyyaledu

3 days lo file 6+ GB reach ayi, disk full ayi service impact ayyindi


Final RCA:
No log rotation configured → Log file grew large → Disk full → Service + Jenkins affected


---

Fix Applied

Unnecessary old logs delete chesaru:


rm -rf /var/log/payment-service.log.1

Immediate logrotate config create chesaru:


nano /etc/logrotate.d/payment-service

/var/log/payment-service.log {
    daily
    missingok
    rotate 7
    compress
    delaycompress
    notifempty
    create 0640 root root
}

Manually rotate cheyyadam ki:


logrotate -f /etc/logrotate.d/payment-service


---

Preventive Actions

1. All services ki logrotate configure cheyyadam mandatory


2. Alerting system lo disk > 80% usage ante alert ravatam


3. Logs ni S3 bucket lo archive cheyyadam (log shipping)


4. Docker container logs ki size limit veyyadam with max-size and max-file


5. Weekly cron job to clean up old logs




---

KK Funda Voice

> "Logs ni ignore cheyyadam ante silent ga ticking time bomb maintain cheyyadam laanti vishayam. Disk full ayithe build, deployment, app – anni impact avutayi. DevOps engineer ki log management kuda oka core skill."
