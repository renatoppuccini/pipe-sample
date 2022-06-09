

oc adm policy add-cluster-role-to-user cluster-admin -z pipeline

oc -n sample-php-cicd apply -f <quay-robot-secret>
oc -n sample-php-cicd secrets link pipeline demo-ocp-pull-secret --for=pull,mount

oc -n sample-php-cicd create secret generic acs-secret \
--from-literal=acs_api_token='' \
--from-literal=acs_central_endpoint=central-stackrox.apps.cluster-dvcqm.dvcqm.sandbox1759.opentlc.com:443

oc apply -k .