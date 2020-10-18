## Cluster setup
For Kubernetes 1.17+, some initial cluster setup is required to install the following:
- CSI VolumeSnapshot beta CRDs (custom resource definitions)
- Snapshot Controller

### Check if cluster components are already installed
Run the follow commands to ensure the VolumeSnapshot CRDs have been installed:
```
$ kubectl get volumesnapshotclasses.snapshot.storage.k8s.io 
$ kubectl get volumesnapshots.snapshot.storage.k8s.io 
$ kubectl get volumesnapshotcontents.snapshot.storage.k8s.io
```
If any of these commands return the following error message, you must install the corresponding CRD:
```
error: the server doesn't have a resource type "volumesnapshotclasses"
```

Next, check if any pods are running the snapshot-controller image:
```
$ kubectl get pods --all-namespaces -o=jsonpath='{range .items[*]}{"\n"}{range .spec.containers[*]}{.image}{", "}{end}{end}' | grep snapshot-controller
quay.io/k8scsi/snapshot-controller:v2.0.1, 
```

If no pods are running the snapshot-controller, follow the instructions below to create the snapshot-controller

__Note:__ The above command may not work for clusters running on managed k8s services. In this case, the presence of all VolumeSnapshot CRDs is an indicator that your cluster is ready for hostpath deployment.

### VolumeSnapshot CRDs and snapshot controller installation
Run the following commands to install these components: 
```shell
# Change to the latest supported snapshotter version
$ SNAPSHOTTER_VERSION=v2.0.1

# Apply VolumeSnapshot CRDs
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/config/crd/snapshot.storage.k8s.io_volumesnapshotcontents.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/config/crd/snapshot.storage.k8s.io_volumesnapshots.yaml

# Create snapshot controller
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/deploy/kubernetes/snapshot-controller/rbac-snapshot-controller.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/deploy/kubernetes/snapshot-controller/setup-snapshot-controller.yaml
```

No Luck...

```
error: the server doesn't have a resource type "volumesnapshotclasses"
colin@cmccarth-mac trilio % kubectl get volumesnapshots.snapshot.storage.k8s.io
error: the server doesn't have a resource type "volumesnapshots"
colin@cmccarth-mac trilio % kubectl get volumesnapshotcontents.snapshot.storage.k8s.io
error: the server doesn't have a resource type "volumesnapshotcontents"
colin@cmccarth-mac trilio % kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml
error: unable to read URL "https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter//config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml", server reported 404 Not Found, status code=404
```



https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml

https://github.com/kubernetes-csi/external-snapshotter

https://kubernetes-csi.github.io/docs/snapshot-controller.html

https://github.com/kubernetes-csi/csi-driver-host-path/blob/master/docs/deploy-1.17-and-later.md

https://kubernetes-csi.github.io/docs/drivers.html

https://github.com/rancher/k3s/issues/85
