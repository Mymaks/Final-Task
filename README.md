# Description
This project allows you to build (every commit to github repository initiated github action for building docker images and pushing it to dockerhub registry) and deploy server.go application on aws eks cluster automaticaly created from terraform. Infrastructure diagram is in the root directory.

# How to

## Create AWS User (IAM) for newcomers or you can use your Access Key for deployment

- Under your AWS account go to the Identity and Access Management (IAM)-> Access management -> Users

- push "Add user" button and following instrustion set new user (attach existing policy - AdministratorAccess or other policies according with project needs).

- Login to AWS via new user.

- In user drop-down user menu choose "Security credentials"

- Create new Accces Key and save it.

- Before to start infrastructure deployment log in into AWS account with Access Key from terminal using following comands:

`export AWS_ACCESS_KEY_ID=Your_Access_Key_ID`
`export AWS_SECRET_ACCESS_KEY= Your_Secret_Access_Key`

## Infrastructure deplyoment from terraform (EKS Cluster + VPC).

- From GitHub CLI clone repository with project:

`gh repo clone Mymaks/Final-Task`

- go to ther terraform directory:

`cd Final-Task/infrastructure/terraform`

- execute comands for infrastructure deployment:

`terraform inint`
`terrarorm plan`
`terraform apply`

- connect to the EKS Cluster and check it is connected:

`aws eks --region eu-west-1 update-kubeconfig --name my-cluster`
`kubectl get svc`

## GitHub actions (CI)
Aplication have been build  with Github Action (every commit of your app changes to github repository initiated github action for rebuilding docker images and pushing it to dockerhub registry)

## Application deployment
- Move to the kubernetes folder:

`cd .. && cd kubernetes`

- Initiate app deployment:

`kubectl apply -f deployment.yaml` # deploying application stored on docker hub from image.
`kubectl apply -f service.yaml` #service allows you to access all replicas through a single IP address or name internaly 
`kubectl apply -f loadbalancer.yaml` # allows you to rich your application from outside.

- Check status of runnig services:

`kubectl get services`

- Initiate port forwarding

`kubectl port-forward service/sever-go-loadbalancer 8080:8080`

- Check external ip adress of running loadbalancer:

`kubectl get service/server-go-loadbalancer |  awk {'print $1" " $2 " " $4 " " $5'} | column -t`

- Verify that you can run your app externally by using expernal ip address of loadbalancer in browser:

`*****.eu-west-1.elb.amazonaws.com:8080`
or
`curl -silent *****.eu-west-1.elb.amazonaws.com:8080`

- Check logs to see that application is up and running correctly:

  `kubectl logs deployment/server-go`

## Delete all resources
- delete loadbalancer, services, deployment:

`kubectl delete servece/server-go-loadbalancer`
`kubectl delete servece/server-go`
`kubectl delete deployment/server-go`

- delete EKS Cluster + VPC:

`cd .. && cd terraform`
`terraform destroy`

# Useful links
- https://docs.aws.amazon.com/eks/latest/userguide/sample-deployment.html
- https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-deployment-tutorial-example-yaml.html
- https://aws.amazon.com/ru/premiumsupport/knowledge-center/eks-kubernetes-services-cluster/
- https://docs.docker.com/language/golang/build-images/
