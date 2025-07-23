# ebs-csi-eks-demo
# ebs-csi-eks-demo

### Theory

```
EKS EBS CSI Driver is a Container Storage Interface (CSI) driver for Amazon Elastic Kubernetes Service (EKS) that allows Kubernetes clusters to use Amazon Elastic Block Store (EBS) volumes for persistent storage.
```


### Associating IAM OIDC Provider with my cluster

```
eksctl utils associate-iam-oidc-provider --cluster eks-ebs-csi-cluster  --approve --region us-east-2
```

### Creating IAM role with the necessary permissions for the EBS CSI Driver and sets up a trust relationship between this IAM role and the Kubernetes service account.

```
eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster eks-ebs-csi-cluster \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --role-only \
  --role-name AmazonEKS_EBS_CSI_Driver_Role \
  --region us-east-2
```

### Install the AWS EBS CSI Driver Addon

```
eksctl create addon --name aws-ebs-csi-driver --cluster eks-ebs-csi-cluster --service-account-role-arn arn:aws:iam::251620460948:role/AmazonEKS_EBS_CSI_Driver_Role --region us-east-2 --force
```

### Lets Check Data Persistence After applying k8s manifests.

```
kubectl exec -it <pod-name> -- /bin/bash
```

```
echo "Writing text inside pod before deletion" > /usr/share/nginx/html/test-file.txt
cat /usr/share/nginx/html/test-file.txt
```

```
kubectl delete pod <pod-name>
```

```
kubectl exec -it <pod-name> -- cat /usr/share/nginx/html/test-file.txt
```
