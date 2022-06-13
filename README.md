
# Preparation

1. Create a user named demouser on quay
2. Create a new organization named demo
3. Create a robot account and download the K8s secret
4. Run the following commands to import the secret
```
oc new-project sample-php-cicd
oc new-project sample-php
oc create -f demo-ocp-secret.yml --namespace=sample-php-cicd
oc create -f demo-ocp-secret.yml --namespace=sample-php

oc patch serviceaccount pipeline \
  -p '{"secrets": [{"name": "demo-ocp-pull-secret"}]}' \
  --namespace=sample-php-cicd

oc secrets link default demo-ocp-pull-secret --for=pull -n sample-php

```
5. Create a new private repository named sample-php.
6. Add write permissions to the robot user. 


4. Create a secret for access to RHACS central. Open the RHACS web user interface and select “Platform Configuration” from the left-hand side menu, then select "Integrations". Scroll down to the section for authentication tokens and select “StackRox API Token.” Press the + sign in the top right corner and select the token role of “Continuous Integration.” Give the token a name and press the green button marked “Generate.” 

Press the green copy symbol and then paste it into the following command:

```
oc create secret generic acs-secret \
--from-literal=acs_api_token='<token from above step>' \
--from-literal=acs_central_endpoint='<url-for-rhacs-central-server>:443' \
--namespace=sample-php-cicd
```

The URL that you put into the above command does not have http:// or https:// on the front of it.

5. Change latest policy to fail on Build: Platform Configuration -> System Policies -> Seach for "Latest" -> Edit -> Next -> Next -> Next -> Switch Build to ON -> Save
6. Add permissions for pipeline user:

```
oc adm policy add-cluster-role-to-user cluster-admin -z pipeline -n sample-php-cicd
```

# Part 1 (CI): Simple CI process - Latest tag error

[NOTE]
Objectives: 
1. Show how a simple CI tekton pipeline looks like
2. Demonstrate how ACS static scan can be used in a pipeline to ensure policies are met.

[PROCESS]
1. Fork this repo
Run the following commands to create the Pipeline
```
oc apply -k ci/
```
2. Change parameter IMAGE from ci/PipelineRun/build-push-latest.yaml to relfect the quay URL.
2. Run the PipelineRun. It will fail due to the latest tag.

```
oc apply -f ci/PipelineRun/pvc.yaml
oc apply -f ci/PipelineRun/build-push-latest.yaml 
```

3. Now fix it by commenting the following line from k8s\deployment.yaml manifest:
```
#          image: quay.io/gfontana/sample-php:latest
```

And changing it to v1.0:
```
          image: quay.io/gfontana/sample-php:v1.0 
```

4. Commit the changes.
5. Run the `oc apply -f ci/PipelineRun/build-push-v1.0.yaml` command. Now it should finish successfully.

# Part 2 (CI): Show Quay/Security Scanner

[NOTE]
Objectives: 
1. Demonstrate how easy is to get Clair security scanner working/scanning the image we just pushed.
2. Fix the issue and run the pipeline again.

[PROCESS]
1. Acess the repository on Quay
2. Click over the security scan
3. Show the detailed vulnerabilities
4. To fix the issues change the Dockerfile from:
```
FROM registry.access.redhat.com/ubi8/ubi-minimal:8.3
```

To:
```
#FROM registry.access.redhat.com/ubi8/ubi-minimal
```

5. Commit and push the changes and rerun the CI pipeline.
6. Access Quay and see that the security scan now is "Passed"

# Part 3 (CD): Deployment pipeline

[NOTE]
Objectives: 
1. Demonstrate deployment with Tekton & ArgoCD

[PROCESS]
oc apply -k cd/

Run pipeline from web console
