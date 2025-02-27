# Troubleshooting High Memory Usage on Ubuntu 24.04 VM Running NGINX

## Issue
- The VM is using **99% of memory**.
- It only runs **NGINX as a load balancer**.

## Steps to Troubleshoot
### 1. Check Memory Usage
```bash
free -h
```
- This shows total, used, and free memory.
- If **swap usage** is also high, the system is running out of RAM.

### 2. Find Processes Using Too Much Memory
```bash
ps aux --sort=-%mem | head -10
```
- If **NGINX is using a lot of memory**, it may need tuning.

### 3. Check NGINX Worker Process Memory Usage
```bash
pgrep -l nginx
pmap $(pgrep nginx) | tail -n 1
```
- If NGINX workers use too much memory, we should adjust configurations.

---

## Possible Problems & Fixes

### Case 1: Too Many Connections Overloading Memory
**Cause:**
- NGINX is handling **too many** active connections.

**Impact:**
- VM runs out of memory → slow response → **service crashes**.

**Fix:**
1. Check connection count:
```bash
netstat -anp | grep nginx | wc -l
```
2. Reduce **worker connections** in `/etc/nginx/nginx.conf`:
```nginx
worker_processes auto;
worker_rlimit_nofile 65535;

events {
    worker_connections 2048;
    multi_accept on;
}
```
3. Restart NGINX:
```bash
sudo systemctl restart nginx
```
4. Check memory again:
```bash
free -h
```

---

### Case 2: Memory Leak or Large Cache Usage
**Cause:**
- NGINX **caching too much** or a **memory leak**.

**Impact:**
- Memory usage keeps growing → **VM crashes**.

**Fix:**
1. Check cache size:
```bash
sudo du -sh /var/cache/nginx/
```
2. Disable buffering in `/etc/nginx/nginx.conf`:
```nginx
proxy_buffering off;
fastcgi_buffering off;
```
3. Restart NGINX:
```bash
sudo systemctl restart nginx
```