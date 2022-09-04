Simple ArgoCD plugin usage:

kubectl -n argocd get cm argocd-cm -o yaml
apiVersion: v1
data:
  configManagementPlugins: |
    - name: kustomized-helm
      generate:
        command: [sh, -c]
        args: ["DOCKER_REG_IP=$(kubectl -n registry get svc registry -o jsonpath={.spec.clusterIP}) && sed -i \"s/DOCKER_REGISTRY_IP/$DOCKER_REG_IP/g\" kustomization.yaml | helm template $ARGOCD_APP_NAME --namespace $ARGOCD_APP_NAMESPACE . > all.yaml && kustomize build"]
    - name: kustomized
      generate:
        command: [sh, -c]
        args: ["DOCKER_REG_IP=$(kubectl -n registry get svc registry -o jsonpath={.spec.clusterIP}) && sed -i \"s/DOCKER_REGISTRY_IP/$DOCKER_REG_IP/g\" kustomization.yaml | kustomize build"]
(from https://github.com/argoproj/argo-cd/issues/5202#issue-781015743)

# notes
How to specify remote Jenkins path in DSL plugin for multibranch pipeline
https://www.jvt.me/posts/2019/12/06/jenkins-job-dsl-multibranch-external-script/

Script to populate Vault policies from files https://www.hashicorp.com/blog/codifying-vault-policies-and-configuration

Test DB backups - https://www.timescale.com/blog/how-i-learned-to-stop-worrying-and-love-postgresql-on-kubernetes-continuous-backup-restore-validation-on-timescale-forge/

Config file examples for many CI systems in one place:
https://testdriven.io/blog/faster-ci-builds-with-docker-cache/

Docker CodeBuilder:
https://github.com/aws-quickstart/quickstart-examples/tree/main/samples/cloudformation-codebuild-container

Note healthchecks for c7rs (used during tests) https://github.com/openzipkin/brave-example/blob/master/build-bin/docker/docker_push

Simple bash command wait script:
```
#!/bin/bash

name=$1
max=$2
interval=$3

[ -z "$1" ] && echo "Usage example: bash wait-for-service.sh mysql 5 1"
[ -z "$2" ] && max=30
[ -z "$3" ] && interval=1

echo "Waiting for service '$name' to become healthy, checking every $interval second(s) for max. $max times"

while true; do 
  ((i++))
  echo "[$i/$max] ..."; 
  status=$(docker inspect --format "{{json .State.Health.Status }}" "$(docker ps --filter name="$name" -q)")
  if echo "$status" | grep -q '"healthy"'; then 
   echo "SUCCESS";
   break
  fi
  if [ $i == $max ]; then 
    echo "FAIL"; 
    exit 1
  fi 
  sleep $interval; 
done
```

HC Vault admin policies:

https://stackoverflow.com/questions/63516147/root-policy-in-hcl-for-hashicorp-vault
