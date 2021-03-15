# Tanzu Postgres operator for Kubernetes


## Create bundle

### Vendir the sources
To update the upstream sources:

```
vendir sync
```

### To create the imgpkg bundle for relocation

```
kbld --file . --imgpkg-lock-output .imgpkg/images.yml
```


## Deploy operator
Create your own configuration file and use it:

```
ytt -f values.CUSTOM.yaml -f upstream -f overlays --ignore-unknown-comments | kubectl apply -f -
```

Using kapp:
```
ytt -f values.CUSTOM.yaml -f upstream -f overlays --ignore-unknown-comments | kapp deploy -a postgres-operator -n default  -f -
```