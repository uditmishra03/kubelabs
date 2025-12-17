# CKA Troubleshooting – Deployment UP-TO-DATE = 0 (Paused Deployment)

## Problem Summary

Deployment `black-cka25-trb` in `cluster1` shows:

- `UP-TO-DATE = 0`
- Pods are running
- Deployment is not progressing

Goal:
- Troubleshoot the issue
- Fix it
- Ensure the deployment becomes **up to date**

---

## Symptoms Observed

```bash
kubectl get deploy black-cka25-trb
