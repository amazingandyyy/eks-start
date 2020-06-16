# eks-start

Learn EKS

## using eksctl to create EKS cluster

- [eksctl](https://eksctl.io/) management basic

```sh
$ eksctl create cluster -f eks-config.yaml
$ eksctl create nodegroup --config-file=eks-config.yaml --include='ng-mixed' # add nodegroupd, scale up
$ eksctl delete nodegroup --config-file=eks-config.yaml --include='ng-mixed' --approve # it will drain nodegroup, then scale down
```

## cluster autoscaler
  - [kubernetes/autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)
  - runs as deployment
  - nodegroup with multi-AZ(for stateless wortkloads) or single-AZ(for stateful workloads)
  - mixture of on-demand and spot-instance possible  

### deploy

- deploy from dpeloyment yaml
  ```sh
  $ kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
  $ kubectl -n kube-system annotate deployment.apps/cluster-autoscaler cluster-autoscaler.kubernetes.io/safe-to-evict="false" --overwrite
  ```
- edit deployment and set your EKS cluster name:
  ```sh
  kubectl -n kube-system edit deployment.apps/cluster-autoscaler
  ```
  - set the image version at property ```image=k8s.gcr.io/cluster-autoscaler:vx.yy.z```  
  - set your EKS cluster name at the end of property

## ControlPlane logging to Cloudwatch

- logs types
  - api
  - audit
  - authenticator
  - controllerManager
  - scheduler
- view logs
  - each log type creates its own cloudwatch log stream
  - name prefix: `/aws/eks/cluster-name/`
- installation
  - add `cloudWatch` to `ClusterConfig` yaml file
  - `eksctl utils update-cluster-logging --config-file eks-course.yaml --approve`

- Cloudwatch metrics
  - add IAM policy for access to cloudwatch agent to nodegroup
