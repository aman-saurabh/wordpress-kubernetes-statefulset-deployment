# wordpress-kubernetes-statefulset-deployment
A sample project to deploy statefulset with kubernetes.

***********************************************************************************************************
For this project to work properly we need to ensure few things: 

1.) For load balancer to work with AWS EKS cluster :-
Install and setup AWS Load Balancer Controller for EKS cluster :-
For this use the following link :- 

https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html

But for it to work you will also need to Create an IAM OIDC provider for your cluster.You can use the following link for this purpose.

https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html

Apart from that we also need to set some tags in all subnets.
You can check following links for help and details : 

https://www.youtube.com/watch?v=3WbEt_sfTWU

https://aws.amazon.com/premiumsupport/knowledge-center/eks-load-balancer-controller-subnets/

https://aws.amazon.com/premiumsupport/knowledge-center/eks-vpc-subnet-discovery/

2.) For dynamic provisioning of persistent volumes to work :-
We will need to setup Amazon EBS CSI driver. You can check following link for help.

https://aws.amazon.com/premiumsupport/knowledge-center/eks-persistent-storage/

***********************************************************************************************************

Note :- Here we have also created pvol.yaml to create persistent volume manually. But this will not work with statefulsets, so we have setup dynamic provisioning of persistent volume.

Create a namespace :-
kubectl create namespace stateful-deployment

Then create Storage class :-
kubectl apply -f gp2-storage-class.yaml --namespace=stateful-deployment

Then check if storage clas is created successfully :-
kubectl get storageclasses --all-namespaces

Then create persistent volume claim :-
kubectl apply -f pvc.yaml --namespace=stateful-deployment

Verify that persistent volume claim is ccreated successfully :-
kubectl get pvc --namespace=stateful-deployment

Create Kubernetes secrets to store passwords :-
kubectl create secret generic mysql-pass --from-literal=password=mysql-pw --namespace=stateful-deployment

Verify that Kubernetes secrets is created successfully :-
kubectl get secrets --namespace=stateful-deployment

Now create service and deployment for mysql :-
kubectl apply -f mysql.yaml --namespace=stateful-deployment

Verify that service and deployment is created successfully :-
kubectl get pods -o wide --namespace=stateful-deployment

Now create service and deployment for wordpress :-
kubectl apply -f wordpress.yaml --namespace=stateful-deployment

Verify that service and deployment is created successfully :-
kubectl get pods -o wide --namespace=stateful-deployment

Now you can go to Load balancers(Inside EC2 section), select the newly created load balancer for your service and copy the DNS URL and enter that in the browser. DNS URL should be something like below:

a5re9133c0eed943gh46gfa9c542-6545459600.ap-south-1.elb.amazonaws.com

So Enter the following URL in the browser and you should be able to visit the workpress site we deployed. 
http://a5re9133c0eed943gh46gfa9c542-6545459600.ap-south-1.elb.amazonaws.com
