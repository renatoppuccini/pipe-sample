
# 1. Prep

1. Create a user named demo on quay
2. Create a robot account and download the K8s secret
3. Run the following commands to import the secret
```
oc new-project sample-php-cicd
oc create -f demo-ocp-secret.yml --namespace=sample-php-cicd
```
?????4. Change the following manifest with the quay url

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

# 1 Run first with old UBI version

1. Fork this repo
Run the following commands to create the Pipeline
```
oc apply -k ci/
```

2. Run the PipelineRun:

```
oc apply -f ci/PipelineRun/pvc.yaml
oc apply -f ci/PipelineRun/build-push-latest.yaml 
```

