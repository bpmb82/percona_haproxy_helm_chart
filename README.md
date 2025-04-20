# percona-haproxy_helm_chart

Helm chart to deploy [percona-haproxy](https://github.com/bpmb82/percona-haproxy)

## Usage

You should supply the following environment variables using a values.yml file:

```
environment:
  - name: PRIMARY_SERVERS
    value: "pg1:10.0.8.20:5432,pg2:10.8.0.21:5432" # Comma separated list of <hostname>:<ip_address>:<port> for use in the primary section.
  - name: SECONDARY_SERVERS
    value: "pg1:10.0.8.20:5432,pg2:10.8.0.21:5432" # Comma separated list of <hostname>:<ip_address>:<port> for use in the standbys section.
```

#### NOTE: the \<port> variable is optional, defaults to 5432

Deploy the chart as follows:

```
helm repo add percona-haproxy https://bpmb82.github.io/percona_haproxy_helm_chart
helm install percona-haproxy percona-haproxy/percona-haproxy --values=values.yml
```

You can then define the IngressRoute in a Traefik ingress route to get to the stats page:

```
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: haproxy-stats
  namespace: default
spec:
  entryPoints:
    - websecure
  routes:
  - match: "Host(`haproxy_stats.example.com`)"
    kind: Rule
    services:
      - name: percona-haproxy
        port: 7000
```