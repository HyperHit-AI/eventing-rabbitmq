## Build and push image
```shell
make ko 
./bin/ko publish --local knative.dev/eventing-rabbitmq/cmd/receive_adapter

docker tag ko.local/receive_adapter-7332e64183bef13ff5d187914376d638:bb63c6fe1b680b4439c3dcb32f0e5c96547094eccdd73bb96251bf16e39e06a4 your-repository/knative.dev/eventing-rabbitmq/cmd/receive_adapter:fixed-content-encoding

kubectl edit deploy rabbitmq-controller-manager -n knative-sources
```
## Replace image
like this
```yaml
#...
env:
- name: SYSTEM_NAMESPACE
    valueFrom:
    fieldRef:
        fieldPath: metadata.namespace
- name: METRICS_DOMAIN
    value: knative.dev/sources
- name: CONFIG_OBSERVABILITY_NAME
    value: config-observability
- name: RABBITMQ_RA_IMAGE
    # value: gcr.io/knative-releases/knative.dev/eventing-rabbitmq/cmd/receive_adapter@sha256:f528504fe914b09bf41c563240b1239c8affb270736b83e4b6d979b107da6b03
    value: your-repository/knative.dev/eventing-rabbitmq/cmd/receive_adapter:fixed-content-encoding # <-- Replace with your own image
securityContext:
allowPrivilegeEscalation: false
#...
```
## Restart the rabbitmq-controller-manager
```shell
kubectl rollout restart deploy rabbitmq-controller-manager -n knative-sources

kubectl delete -f your-rabbitmqsource.yaml

kubectl apply -f your-rabbitmqsource.yaml
```

## Do not ovverwrite the vendor directory, e.g. go mod vendor!!!
## This is a temporary solution