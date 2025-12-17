# CKA RBAC Question – ServiceAccount Access to Namespaces

## Problem Statement

We have the following Kubernetes RBAC objects already created:

- ServiceAccount: `green-sa-cka22-arch`
- ClusterRole: `green-role-cka22-arch`
- ClusterRoleBinding: `green-role-binding-cka22-arch`
- Cluster: `cluster1`

### Requirement

Update the permissions of the ServiceAccount so that it can **only `get` all namespaces** in the cluster.

---

## Key Concepts Tested (CKA Traps)

- `namespaces` is a **cluster-scoped** resource.
- Cluster-scoped resources **require a ClusterRole**, not a Role.
- `namespaces` belongs to the **core API group**, not `apps`.
- Core API group is represented as an **empty string (`""`)**.
- Verb must be **only `get`** (not `list`, `watch`, or `*`).

---

## Incorrect Configuration (Common Mistake)

```yaml
apiGroups:
- apps
resources:
- namespaces
verbs:
- get
