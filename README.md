1. Create AWS User (IAM)  :
- Under your AWS account go to the Identity and Access Management (IAM)-> Access management -> Users
- push "Add user" button and following instrustion set new user (attach existing policy - AdministratorAccess).

- Login to AWS via new user.

- In user drop-down menu choose "Security credentials"

- Create new Accces Key and save it.

- Before to start infrastructure deployment log in into AWS account with Access Key from terminal using following comands:
  ~ export AWS_ACCESS_KEY_ID=Your_Access_Key_ID
  ~ export AWS_SECRET_ACCESS_KEY= Your_Secret_Access_Key

2. Infrastructure deplyoment from terraform (EKS Cluster + VPC).
- From GitHub CLI clone repository with project:
  ~ gh repo clone Mymaks/Final-Task

- go to ther terraform directory:
  ~ cd Final-Task/infrastructure/terraform

- execute comands for infrastructure deployment:
  ~ terraform inint
  ~ terraform apply

- connect to the EKS Cluster and check it is connected:
  ~ aws eks --region eu-west-1 update-kubeconfig --name my-cluster
  ~ kubectl get svc

3. Application deployment
- Move to the kubernetes folder:
  ~ cd .. && cd kubernetes

- Initiate app deployment:
  ~ kubectl apply -f deployment.yaml # deploying application stored on docker hub from image.
  ~ kubectl apply -f service.yaml #service allows you to access all replicas through a single IP address or name internaly 
  ~ kubectl apply -f loadbalancer.yaml # allows you to rich your application from outside.

- Check status of runnig services:
  ~ kubectl get services

- Initiate port forwarding
  ~ kubectl port-forward service/sever-go-loadbalancer 8080:8080

- Check external ip adress of running loadbalancer:
  ~ kubectl get service/server-go-loadbalancer |  awk {'print $1" " $2 " " $4 " " $5'} | column -t

- Verify that you can run your app externally by using expernal ip address of loadbalancer in browser:
  ~ *****.eu-west-1.elb.amazonaws.com:8080
  or
  ~ curl -silent *****.eu-west-1.elb.amazonaws.com:8080

- Check logs to see that application is up and running correctly:
  ~ kubectl logs deployment/server-go

4. Delete all resources.
- delete loadbalancer, services, deployment:
~ kubectl delete servece/server-go-loadbalancer
~ kubectl delete servece/server-go
~ kubectl delete deployment/server-go

- delete EKS Cluster + VPC:
~ cd .. && cd terraform
~ terraform destroy

