### **I modified a configmap/secret but the new config is not replicated to pods**

This is a known issue that [Pulp team is discussing](https://github.com/pulp/pulp-operator/issues/521) what will be the best way to handle it. The [Kubernetes community does not have a consensus](https://github.com/kubernetes/kubernetes/issues/22368) about it too.

One of the reasons that we don't want to put the pod restart logic (to propagate the new configuration) in operator is because it can cause downtime in case of error and we would need to automate the rollback or fix processes, which would probably bring other issues instead of being helpful.



### **How can I fix the "*Multi-Attach error for volume "my-volume" Volume is already used by pod(s) my-pod*"?**

    Warning  FailedAttachVolume  44m                   attachdetach-controller  Multi-Attach error for volume "my-volume" Volume is already used by pod(s) my-pod

The `Multi-Attach` error happens when the deployment is configured to mount a RWO volume and the scheduler tries to run a new pod in a different node. To fix the error and allow the new pod to be provisioned, delete the old pod so that the new one will be able to mount the volume.

Another common scenario that this issue happens is if a node lose communication with the cluster and the controller tries to provision a new pod. Since the communication of the failing node with the cluster is lost, it is not possible to dettach the volume before trying to attach it again to the new pod.

To avoid this issue, it is recommended to use RWX volumes. If it is not possible, modify the `strategy` type from Pulp CR as `Recreate`.