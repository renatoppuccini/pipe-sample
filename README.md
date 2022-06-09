# Fixing latest tag

2. Replace the latest in k8s/deployment.yaml to v1.0

```
#          image: quay.io/gfontana/sample-php:latest
          image: quay.io/gfontana/sample-php:v1.0        
```

Commit and push changes

Run the following PipelineRun:

```
oc apply -f ci/PipelineRun/build-push-v1.0.yaml 
```

