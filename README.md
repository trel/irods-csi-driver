## iRODS CSI Driver

[iRODS](https://irods.org) Container Storage Interface (CSI) Driver implements the [CSI Specification](https://github.com/container-storage-interface/spec/blob/master/spec.md) to provide container orchestration engines (like [Kubernetes](https://kubernetes.io/)) iRODS access.

### CSI Specification Compatibility

iRODS CSI Driver only supports CSI Specification Version v1.2.0 or higher.

### Features

iRODS CSI Driver relies on external iRODS clients for mounting iRODS collections.
| Driver Type | iRODS Client     | Server Requirements             |
|-------------|------------------|---------------------------------|
| irodsfuse   | iRODS FUSE       | no                              |
| webdav      | DavFS2           | require [iRODS-WebDAV](https://github.com/DICE-UNC/irods-webdav) or [Davrods](https://github.com/UtrechtUniversity/davrods) |
| nfs         | NFS (nfs-common) | require [NFS-RODS](https://github.com/irods/irods_client_nfsrods)                |

Currently, iRODS CSI Driver only supports static provisioning.

### Installation

Deploy the stable driver:

```sh
kubectl apply -k "github.com/cyverse/irods-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
```

Deploy the development driver:
```sh
kubectl apply -k "github.com/cyverse/irods-csi-driver/deploy/kubernetes/overlays/dev/?ref=master"
```

Verify the driver installation:
```sh
kubectl get csinodes -o jsonpath='{range .items[*]} {.metadata.name}{": "} {range .spec.drivers[*]} {.name}{"\n"} {end}{end}'
```

### Uninstallation

Uninstall the stable driver:
```sh
kubectl delete -k "github.com/cyverse/irods-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
```

Uninstall the development driver:
```sh
kubectl delete -k "github.com/cyverse/irods-csi-driver/deploy/kubernetes/overlays/dev/?ref=master"
```

### Mount

Define Storage Class (SC):
```sh
kubectl apply -f "examples/kubernetes/irodsfuse_static_provisioning/storageclass.yaml"
```

Verify the Storage Class definition:
```sh
kubectl get sc
```

Define Persistent Volume (PV):
```sh
kubectl apply -f "examples/kubernetes/irodsfuse_static_provisioning/pv.yaml"
```

Verify the Persistent Volume definition:
```sh
kubectl get pv
```

Claim Persistent Volume (PVC):
```sh
kubectl apply -f "examples/kubernetes/irodsfuse_static_provisioning/pvc.yaml"
```

Verify the Persistent Volume Claim:
```sh
kubectl get pvc
```

Execute Application with Volume Mount:
```sh
kubectl apply -f "examples/kubernetes/irodsfuse_static_provisioning/app.yaml"
```

### Unmount

Delete Application:
```sh
kubectl delete --grace-period=0 --force -f "examples/kubernetes/irodsfuse_static_provisioning/app.yaml"
```


### References

Following CSI driver implementations were used as references:
- [AWS EFS CSI Driver](https://github.com/kubernetes-sigs/aws-efs-csi-driver)
- [NFS CSI Driver](https://github.com/kubernetes-csi/drivers)
- [Ceph CSI Driver](https://github.com/ceph/ceph-csi)

Many code parts in the driver are from **AWS EFS CSI Driver**.

Following resources are helpful to understand the CSI driver implementation:
- [CSI Specification](https://github.com/container-storage-interface/spec/blob/master/spec.md)
- [Kubernetes CSI Developer Documentation](https://kubernetes-csi.github.io/docs/)

### License

This library is licensed under the Apache 2.0 License.
