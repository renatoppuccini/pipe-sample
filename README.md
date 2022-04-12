# Fixing Dockerfile.md


1. Replace this code in the Dockerfile

```
RUN microdnf --nodocs -y install httpd php \
  && microdnf clean all \
  && rpm -e $(rpm -qa *rpm*) $(rpm -qa *dnf*) $(rpm -qa *libsolv*) $(rpm -qa *hawkey*) $(rpm -qa yum*)
```

2. Add this code in the end of k8s/deployment.yaml file

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