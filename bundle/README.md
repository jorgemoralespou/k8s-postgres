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

or 

```
ytt -f config --ignore-unknown-comments |  kbld --file - --imgpkg-lock-output .imgpkg/images.yml
```


## Deploy operator
Create your own configuration file and use it:

```
ytt -f config --ignore-unknown-comments | kubectl apply -f -
```

Using kapp:
```
ytt -f config --ignore-unknown-comments | kapp deploy -a postgres-operator -n default  -f -
```

## Create a Carvel Package
Create the bundle package, push it to a registry and create the bundle package manifest:

```
apiVersion: package.carvel.dev/v1alpha1
kind: Package
metadata:
  # Kubernetes name is not used for anything specific but is still required.
  # Use ${spec.publicName}.#{spec.version} as a convetion
  name: tanzu-postgres-operator.1.1.0-vmware0
spec:
  # publicName will be used by consumers of this package.
  # publicName should be unique across all packages, hence we
  # recommend to use fully qualified name similar to what
  # would be used as a name for a CRD.
  publicName: tanzu-postgres-operator
  # version will be used by consumers of this package.
  # must be a valid semantic version string.
  version: "1.1.0-vmware0"
  template:
    spec:
      fetch:
        - imgpkgBundle:
            # replace image registry and repo with one you are using
            image: PACKAGE_RPOSITORY_IMAGE
      template:
        - ytt:
            paths:
              - config/
        - kbld: {}
      deploy:
        - kapp: {}
```