Commands

```
INGRESS_HOST=$(oc get route -n istio-system istio-ingressgateway -o jsonpath='{.spec.host}'/mtls)
curl $INGRESS_HOST

CUSTOMER_POD=$(oc get pods -l app=customer -o jsonpath={.items..metadata.name})
istioctl experimental authz check $CUSTOMER_POD

oc create -f policy.yml
istioctl experimental authz check $CUSTOMER_POD | grep virtual
```