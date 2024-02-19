# Helm Charts for Navicore Projects

```
helm package src/navitain
helm package src/echo-secret
helm repo index .
```

to deploy a package (ie: navitain):

```
helm repo add navicore https://www.navicore.tech/charts/
helm repo update
helm install my-navitain navicore/navitain
helm install echo-secret navicore/echo-secret
```

a more direct approach to installing a package:

```
helm install my-navitain https://www.navicore.tech/charts/navitain-0.1.10.tgz
helm install echo-secret https://www.navicore.tech/charts/echo-secret-0.1.0.tgz
```
