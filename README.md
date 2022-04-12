# Fixing Dockerfile.md


1. Add this code in the end of k8s/deployment.yaml file

```
          resources:
            requests:
              memory: "100Mi"
              cpu: "100m"
            limits:
              memory: "500Mi"
              cpu: "500m"
```

Commit and push changes
