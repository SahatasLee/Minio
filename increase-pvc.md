# Increase PVC

Cannot decrease volume size

1. Edit every pvc.yaml that want to increase
2. Rollout restart minio for pvc to increase size
3. Console at size bar pools click wanted pool list click edit pool button to increase volume size and save

```sh
# minio-pvc.yaml
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi # edit here
```