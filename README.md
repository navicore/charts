# Helm Charts for Navicore Projects

```
helm package src/navitain
helm repo index .
```

to deploy a package (ie: navitain):

```
helm repo add navicore https://www.navicore.tech/charts/
helm repo update
helm install my-navitain navicore/navitain
```

a more direct approach to installing a package:

```
helm install my-navitain https://www.navicore.tech/charts/navitain-0.1.10.tgz
```
