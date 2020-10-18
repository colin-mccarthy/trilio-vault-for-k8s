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




```
git clone https://github.com/kubernetes-csi/csi-driver-host-path
```
Kube 1.18 ran the shell script
```
./deploy.sh 
```




```
colin@cmccarth-mac kubernetes-1.18 % k get events
LAST SEEN   TYPE      REASON             OBJECT                                 MESSAGE
10m         Normal    SuccessfulCreate   statefulset/snapshot-controller        create Pod snapshot-controller-0 in StatefulSet snapshot-controller successful
<unknown>   Normal    Scheduled          pod/snapshot-controller-0              Successfully assigned coder/snapshot-controller-0 to localhost.localdomain
10m         Normal    Pulling            pod/snapshot-controller-0              Pulling image "quay.io/k8scsi/snapshot-controller:v2.0.0-rc4"
10m         Normal    Pulled             pod/snapshot-controller-0              Successfully pulled image "quay.io/k8scsi/snapshot-controller:v2.0.0-rc4"
10m         Normal    Created            pod/snapshot-controller-0              Created container snapshot-controller
10m         Normal    Started            pod/snapshot-controller-0              Started container snapshot-controller
7m48s       Normal    SuccessfulCreate   statefulset/csi-hostpathplugin         create Pod csi-hostpathplugin-0 in StatefulSet csi-hostpathplugin successful
<unknown>   Normal    Scheduled          pod/csi-hostpathplugin-0               Successfully assigned coder/csi-hostpathplugin-0 to localhost.localdomain
7m48s       Normal    Pulling            pod/csi-hostpathplugin-0               Pulling image "k8s.gcr.io/sig-storage/csi-node-driver-registrar:v2.0.1"
7m47s       Normal    SuccessfulCreate   statefulset/csi-hostpath-snapshotter   create Pod csi-hostpath-snapshotter-0 in StatefulSet csi-hostpath-snapshotter successful
<unknown>   Normal    Scheduled          pod/csi-hostpath-snapshotter-0         Successfully assigned coder/csi-hostpath-snapshotter-0 to localhost.localdomain
7m47s       Normal    SuccessfulCreate   statefulset/csi-hostpath-socat         create Pod csi-hostpath-socat-0 in StatefulSet csi-hostpath-socat successful
<unknown>   Normal    Scheduled          pod/csi-hostpath-socat-0               Successfully assigned coder/csi-hostpath-socat-0 to localhost.localdomain
7m47s       Normal    Pulling            pod/csi-hostpath-snapshotter-0         Pulling image "k8s.gcr.io/sig-storage/csi-snapshotter:v3.0.0"
7m46s       Normal    Pulled             pod/csi-hostpathplugin-0               Successfully pulled image "k8s.gcr.io/sig-storage/csi-node-driver-registrar:v2.0.1"
7m46s       Normal    Created            pod/csi-hostpathplugin-0               Created container node-driver-registrar
7m46s       Normal    Started            pod/csi-hostpathplugin-0               Started container node-driver-registrar
7m46s       Normal    Pulling            pod/csi-hostpathplugin-0               Pulling image "k8s.gcr.io/sig-storage/hostpathplugin:v1.4.0"
7m46s       Normal    Pulling            pod/csi-hostpath-socat-0               Pulling image "alpine/socat:1.0.3"
7m44s       Normal    Pulled             pod/csi-hostpath-socat-0               Successfully pulled image "alpine/socat:1.0.3"
7m44s       Normal    Pulled             pod/csi-hostpathplugin-0               Successfully pulled image "k8s.gcr.io/sig-storage/hostpathplugin:v1.4.0"
7m44s       Normal    Created            pod/csi-hostpath-socat-0               Created container socat
7m44s       Normal    Created            pod/csi-hostpathplugin-0               Created container hostpath
7m44s       Normal    Started            pod/csi-hostpath-socat-0               Started container socat
7m44s       Normal    Started            pod/csi-hostpathplugin-0               Started container hostpath
7m44s       Normal    Pulling            pod/csi-hostpathplugin-0               Pulling image "k8s.gcr.io/sig-storage/livenessprobe:v2.1.0"
7m44s       Normal    Pulled             pod/csi-hostpath-snapshotter-0         Successfully pulled image "k8s.gcr.io/sig-storage/csi-snapshotter:v3.0.0"
7m44s       Normal    Created            pod/csi-hostpath-snapshotter-0         Created container csi-snapshotter
7m44s       Normal    Started            pod/csi-hostpath-snapshotter-0         Started container csi-snapshotter
7m42s       Normal    Pulled             pod/csi-hostpathplugin-0               Successfully pulled image "k8s.gcr.io/sig-storage/livenessprobe:v2.1.0"
7m42s       Normal    Created            pod/csi-hostpathplugin-0               Created container liveness-probe
7m42s       Normal    Started            pod/csi-hostpathplugin-0               Started container liveness-probe
2m22s       Warning   FailedCreate       statefulset/csi-hostpath-attacher      create Pod csi-hostpath-attacher-0 in StatefulSet csi-hostpath-attacher failed error: pods "csi-hostpath-attacher-0" is forbidden: error looking up service account coder/csi-attacher: serviceaccount "csi-attacher" not found
2m20s       Warning   FailedCreate       statefulset/csi-hostpath-provisioner   create Pod csi-hostpath-provisioner-0 in StatefulSet csi-hostpath-provisioner failed error: pods "csi-hostpath-provisioner-0" is forbidden: error looking up service account coder/csi-provisioner: serviceaccount "csi-provisioner" not found
2m20s       Warning   FailedCreate       statefulset/csi-hostpath-resizer       create Pod csi-hostpath-resizer-0 in StatefulSet csi-hostpath-resizer failed error: pods "csi-hostpath-resizer-0" is forbidden: error looking up service account coder/csi-resizer: serviceaccount "csi-resizer" not found
```




Links:

https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml

https://github.com/kubernetes-csi/external-snapshotter

https://kubernetes-csi.github.io/docs/snapshot-controller.html

https://github.com/kubernetes-csi/csi-driver-host-path/blob/master/docs/deploy-1.17-and-later.md

https://kubernetes-csi.github.io/docs/drivers.html

https://github.com/rancher/k3s/issues/85
