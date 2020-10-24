# trilio-vault-for-k8s


Pre-flight check

https://docs.trilio.io/kubernetes/appendix/triliovault-for-kubernetes-preflight-check

Install

https://docs.trilio.io/kubernetes/use-triliovault/installing-triliovault#prerequisites-for-triliovault-for-kubernetes

Set Up

https://docs.trilio.io/kubernetes/overview/getting-started


HostPath CSI Driver for TrilioVault for Kubernetes

https://docs.trilio.io/kubernetes/appendix/csi-drivers/hostpath-for-tvk

Kubernetes Triliovault Preflight Checks


https://github.com/triliovault-k8s-issues/triliovault-k8s-issues/blob/master/tools/preflight/README.md


Create Target
```
kubectl create -f s3-target.yaml -n colin
```

Deploy Helm chart in colin namespace
```
helm install cockroachdb-app --values cockroachdb-values.yaml stable/cockroachdb -n colin
```

Verify pods are up
```
colin@cmccarth-mac openshift % k get po -n colin
NAME                                                 READY   STATUS      RESTARTS   AGE
cockroachdb-app-0                                    1/1     Running     0          2m16s
cockroachdb-app-1                                    1/1     Running     0          2m16s
cockroachdb-app-2                                    1/1     Running     0          2m16s
cockroachdb-app-init-dxg7k                           0/1     Completed   0          2m16s
k8s-triliovault-admission-webhook-5794dc775f-mnknt   1/1     Running     0          13m
k8s-triliovault-control-plane-844c5c6697-x5mgf       1/1     Running     0          13m
k8s-triliovault-exporter-bcdd848-ppwb8               1/1     Running     0          13m
```
