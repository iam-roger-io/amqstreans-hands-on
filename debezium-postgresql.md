
```
kind: Project
apiVersion: project.openshift.io/v1
metadata:
  name: postgresql-lab
  uid: c94d22de-dd40-4a09-bf49-cb31ba2911f8
  resourceVersion: '39191'
  creationTimestamp: '2023-06-17T20:07:43Z'
  labels:
    kubernetes.io/metadata.name: postgresql-lab
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/audit-version: v1.24
    pod-security.kubernetes.io/warn: restricted
    pod-security.kubernetes.io/warn-version: v1.24
  annotations:
    openshift.io/description: ''
    openshift.io/display-name: postgresql-lab
    openshift.io/requester: kubeadmin
    openshift.io/sa.scc.mcs: 's0:c26,c10'
    openshift.io/sa.scc.supplemental-groups: 1000670000/10000
    openshift.io/sa.scc.uid-range: 1000670000/10000
spec:
  finalizers:
    - kubernetes
status:
  phase: Active
```

```
oc port-forward $(oc get pods  -o custom-columns=POD:.metadata.name --no-headers --selector name=postgresql) 5432:5432
```
