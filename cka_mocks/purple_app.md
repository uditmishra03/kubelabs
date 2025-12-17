# CKA Troubleshooting – Purple App Service Connectivity Issue

## Problem Statement

**Scenario:** `ssh cluster1-controlplane`

The `purple-app-cka27-trb` pod is an nginx based app on the container port `80`. This app is exposed within the cluster using a `ClusterIP` type service called `purple-svc-cka27-trb`.

There is another pod called `purple-curl-cka27-trb` which continuously monitors the status of the app running within `purple-app-cka27-trb` pod by accessing the `purple-svc-cka27-trb` service using `curl`.

Recently, we started seeing some errors in the logs of the `purple-curl-cka27-trb` pod.

**Task:** Dig into the logs to identify the issue and resolve it.

**Validation:** Are the issues fixed?

---

## Resolution Steps

### Step 1: SSH to the Control Plane
```bash
ssh cluster1-controlplane
```

### Step 2: Check the Curl Pod Logs
First, identify the error by checking the logs of the monitoring pod:
```bash
kubectl logs purple-curl-cka27-trb
```

**Expected Output:** You should see connection errors indicating the service is not reachable.

### Step 3: Verify Pod Status
Check if the purple app pod is running:
```bash
kubectl get pods | grep purple
```

Ensure `purple-app-cka27-trb` is in `Running` state.

### Step 4: Inspect the Service Configuration
```bash
kubectl get svc purple-svc-cka27-trb
kubectl describe svc purple-svc-cka27-trb
```

**Check for:**
- Service type (should be ClusterIP)
- Port and TargetPort values
- Endpoints (should match the pod IP)

### Step 5: Check Service Endpoints
```bash
kubectl get endpoints purple-svc-cka27-trb
```

If endpoints are empty or missing, the service selector might not match the pod labels.

### Step 6: Compare Service Selector with Pod Labels
```bash
# Check service selector
kubectl get svc purple-svc-cka27-trb -o yaml | grep -A 5 selector

# Check pod labels
kubectl get pod purple-app-cka27-trb --show-labels
```

### Step 7: Verify Port Configuration
Check if the service port matches the container port:
```bash
# Check container port in pod
kubectl get pod purple-app-cka27-trb -o yaml | grep -A 5 ports

# Check service port configuration
kubectl get svc purple-svc-cka27-trb -o yaml | grep -A 5 ports
```

### Step 8: Identify the Root Cause
**Common issues to look for:**
- **Port mismatch:** Service `targetPort` doesn't match container port (80)
- **Selector mismatch:** Service selector doesn't match pod labels
- **Wrong service port:** Service port configured incorrectly

### Step 9: Fix the Issue
Based on the issue identified, edit the service:
```bash
kubectl edit svc purple-svc-cka27-trb
```

**If port mismatch:** Update the `targetPort` to `80` (matching the nginx container port)
```yaml
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80  # Ensure this matches the container port
```

**If selector mismatch:** Update the selector to match pod labels
```yaml
spec:
  selector:
    app: purple-app  # Ensure this matches the pod labels
```

### Step 10: Verify the Fix
After making changes, verify the endpoints are now populated:
```bash
kubectl get endpoints purple-svc-cka27-trb
```

### Step 11: Check Curl Pod Logs Again
```bash
kubectl logs purple-curl-cka27-trb --tail=20
```

**Expected Output:** You should now see successful HTTP 200 responses instead of connection errors.

### Step 12: Test Service Connectivity (Optional)
Test the service from within the cluster:
```bash
kubectl run test-pod --image=busybox --rm -it --restart=Never -- wget -O- purple-svc-cka27-trb
```

---

## Common Root Causes & Solutions

| Issue                  | Symptom                    | Solution                                                 |
| ---------------------- | -------------------------- | -------------------------------------------------------- |
| **Port Mismatch**      | Connection refused/timeout | Update service `targetPort` to match container port (80) |
| **Selector Mismatch**  | No endpoints               | Update service selector to match pod labels              |
| **Pod Not Running**    | No endpoints               | Check pod status and restart if needed                   |
| **Wrong Service Port** | Connection fails           | Ensure service `port` is correctly configured            |

---

## Verification Checklist

- [ ] Pod `purple-app-cka27-trb` is Running
- [ ] Service `purple-svc-cka27-trb` exists and is type ClusterIP
- [ ] Service endpoints are populated with pod IP
- [ ] Service selector matches pod labels
- [ ] Service targetPort matches container port (80)
- [ ] Curl pod logs show successful connections
- [ ] No error messages in `purple-curl-cka27-trb` logs

---

## Key Kubernetes Concepts

- **ClusterIP Service:** Internal cluster service accessible only within the cluster
- **Service Selector:** Matches pods using labels
- **targetPort:** The port on the pod/container that the service forwards traffic to
- **port:** The port the service exposes
- **Endpoints:** Automatically created based on pods matching the service selector

---

## Symptoms Observed

From curl pod logs:
```text
Not able to connect to the nginx app on http://purple-svc-cka27-trb
