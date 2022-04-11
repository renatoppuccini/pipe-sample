

oc adm policy add-cluster-role-to-user cluster-admin -z pipeline

oc apply -f <quay-robot-secret> -n sample-php-cicd
oc -n sample-php-cicd secrets link pipeline demo-ocp-pull-secret --for=pull,mount

oc apply -k .