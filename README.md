# OpenShift Console UI On Non OpenShift K8S Distributions

Image: quay.io/openshift/origin-console:4.15.0  
Make sure you use the 4.15.0 image tag because it is the last tag supported for non openshift distributions.  
The OpenShift Console UI will perform the API calls through the serviceAccount that runs the pod, Which means you have to give cluster-admin to the related serviceAccount.  

As for authentication. There are 2 main options.

1\. **Disabled**  
If you are choosing to disable the authentication, be aware that everyone that has network access to the openshift-console ingress would able to perform any cluster-admin tasks from the UI so you may wanna add some basic authentication through your ingress controller or something.   
To use disabled authentication method, use these commands and args in the deployment.
```
command: ["/opt/bridge/bin/bridge", "--public-dir=/opt/bridge/static", "-user-settings-location=localstorage"]
args:
  - -user-auth=disabled
```

2\. **OIDC** (Keycloak/Okta/...)  
The cluster must accept OIDC users within the apiserver or kube-oidc-proxy.    
To use OIDC authentication method, use these commands and args in the deployment.
```
command: ["sh", "-c"]
args:
  - openssl rand -out /tmp/key 32 &&
    /opt/bridge/bin/bridge
    --public-dir=/opt/bridge/static
    -user-settings-location=localstorage
    -cookie-authentication-key-file=/tmp/key
    -cookie-encryption-key-file=/tmp/key
    -user-auth=oidc
    -user-auth-oidc-issuer-url=<ISSUER_URL>
    -user-auth-oidc-client-id=<CLIENT_ID>
    -user-auth-oidc-client-secret=<CLIENT_SECRET>
    -base-address=<BASE_ADDRESS>
```
