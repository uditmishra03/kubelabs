# CKA – Services & DNS Resolution (nginx + BusyBox)

## Question Summary

- Create an nginx Pod named `nginx-resolver-cka06-svcn`.
- Expose it internally using a Service named `nginx-resolver-service-cka06-svcn`.
- Verify DNS resolution for:
  - Service name.
  - Pod name.
- Use `busybox:1.28` for DNS lookup.
- Record outputs on `cluster1-controlplane`:
  - `/root/CKA/nginx.svc.cka06.svcn`
  - `/root/CKA/nginx.pod.cka06.svcn`

---

## Step 1: Create nginx Pod

```bash
kubectl run nginx-resolver-cka06-svcn \
  --image=nginx \
  --restart=Never \
  --labels=app=nginx-resolver
