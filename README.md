# g8s-oauth2-proxy

Reverse OAuth2 Proxy that handles authentication for GS web frontends.

It is build upon the [bitly/oauth2_proxy](https://github.com/bitly/oauth2_proxy), with the configuration:

```bash
--cookie-expire=0h30m0s
--email-domain="giantswarm.io"
--provider=google
```

More options can be found [here](https://github.com/bitly/oauth2_proxy#command-line-options).


## Current supported Services at GS: 
- G8s-Prometheus
- G8s-Alertmanager
- G8s-Kibana

## Add authentication/authorization to a web frontend

1. Add following annotations to the existing ingress:
```yaml
annotations:
  nginx.ingress.kubernetes.io/auth-signin: https://$host/oauth2/start
  nginx.ingress.kubernetes.io/auth-url: https://$host/oauth2/auth
```

2. Create a new oauth2 ingress together with the existing ingress
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: { Your.Service.Name }-oauth2-proxy
  namespace: kube-system
spec:
  rules:
  - host: {{ Your.Service.URL }}
    http:
      paths:
      - backend:
          serviceName: oauth2-proxy
          servicePort: 4180
        path: /oauth2
```

If SSL is enabled, add the same certificate as the existing ingress, to the oauth2 ingress.

3. Add the (callback-) urls for the new Service to the google Oauth2 Client

The google client can be found here: https://console.developers.google.com/apis/credentials.
