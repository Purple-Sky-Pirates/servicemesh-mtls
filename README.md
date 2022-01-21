Commands

```
INGRESS_HOST=$(oc get route -n istio-system istio-ingressgateway -o jsonpath='{.spec.host}'/mtls)
curl $INGRESS_HOST

CUSTOMER_POD=$(oc get pods -l app=customer -o jsonpath={.items..metadata.name})
istioctl experimental authz check $CUSTOMER_POD

oc create -f policy.yml
istioctl experimental authz check $CUSTOMER_POD | grep virtual

oc exec $(oc get pods -o name | grep sleep) -- curl customer:8080
curl $INGRESS_HOST
```

service accounts for each pod
```
oc create serviceaccount customer
oc create serviceaccount recommendation
oc create serviceaccount preference

oc set serviceaccount deployment customer customer
oc set serviceaccount deployment recommendation recommendation
oc set serviceaccount deployment preference preference

CUSTOMER_POD=$(oc get pods -l app=customer -o name)

Verify the customer pod uses a unique SVID

oc exec $CUSTOMER_POD -c istio-proxy -- curl -s http://127.0.0.1:15000/config_dump  | jq -r .configs[5].dynamic_active_secrets[0].secret | jq -r .tls_certificate.certificate_chain.inline_bytes | base64 --decode | openssl x509 -text -noout | grep "X509v3 Subject" -A 

Verify that pods can communicate using the unique identities
curl $INGRESS_HOST

oc exec $POD_NAME -c istio-proxy -- curl -s http://127.0.0.1:15000/config_dump  | jq -r .configs[5].dynamic_active_secrets[0].secret | jq -r .tls_certificate.certificate_chain.inline_bytes | base64 --decode > certificate-chain.pem



```