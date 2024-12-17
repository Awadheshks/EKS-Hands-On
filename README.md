# EKS Workshop

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)

## Enhanced Observability Configuration:
#### Add AWS CloudWatch Observability as an "add-on" to enable container sights within the cluster.


1.	Create IAM role.
 a.	Log in to the AWS Management Console: Open the IAM Console.
 b.	Create a New Role:
 Trusted Entity: Select AWS Service.
 Choose EC2 for "Service or use case".
 Click Next
 c.	In "Add permissions" select "AdministratorAccess" policy
 Click Next
 d.	Add Role Name as "EKS-Workshop-Admin-Role"
 Click "Create Role"
 e.	Copy the ARN for the role. Need it in the next step.

2.	IDE and EKS Cluster SetUp
   Run https://catalog.workshops.aws/karpenter/en-US/workshop-setup#in-your-own-account
  	
4.	Open IDE (Get the URL and user credentials from Cloudformation Stack Outputs section)
5.  Create folder and subfolder and copy all related files like detailed-resume.html,Dockerfile,headshot.jpg,loadbalancerservice.yaml
6.	Then run command: cd awscourseforbeginners/resumefromkubernetes
7.	docker build -t custom-httpd .
8.	docker images - Copy the image-id of the custom-httpd image and keep in a notepad
9.	Create a free userid password at https://hub.docker.com/
10.	Create a repository in dockerhub with name custom-httpd
     NOTE: Docker repository Visibility must be set to Public, otherwise docker pull image will fail as there is no docker secret.
11.	Go back to IDE, login to dockerhub from CLI by running command : docker login --username=<GIVE DOCKERHUB USERNAME> . At prompt give password
12. Tag your container image by running command: docker tag <IMAGE_ID OF THE custom-httpd> <DOCKERHUB USERID>/custom-httpd
13. Push your container image by running command: docker push > <DOCKERHUB USERID>/custom-httpd
14. Open loadbalancerservice.yaml, and under containers, replace “image: httpd” with “image: <DOCKERHUB USERID>/custom-httpd” .Save the file 
15.	Apply this manifest file by running command : kubectl apply -f loadbalancerservice.yaml
16.	After 5 mins loadbalancer should be up and running. Get loadbalancer URL by running command: kubectl get service
17.	Copy the loadbalancer URL (Under EXTERNAL-IP column), and open it in a webpage
18.	Update the html, and include ur pic in headshot.jpg and re-run from building docker image (Step1)

### Now we will enable observability

1. Go to EKS console and select your cluster
2.	If you see this message on top, click “Create access entry”, and go through the steps, choose EKSadmin policy from drop down,then click Add-Ons tab ,click “Get more add-ons”
3.	Scroll down and select “Amazon CloudWatch Observability” , Scroll down further and click “Next”
4.	We need to create the IRSA for the Cloudwatch Agent
   a.	Click “CloudWatch Observability add-on User Guide” - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Observability-EKS-addon.html
   b.	Run steps 1 and 2 in this page https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Observability-EKS-addon.html#install-CloudWatch- 
       Observability-EKS-addon-serviceaccountrole 
Note: Replace my-cluster-name with the name of your cluster. You can leave the service-account name as is.

{

  aws iam attach-role-policy \
--role-name EKS-Workshop-Admin-Role \
--policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy

}

eksctl utils associate-iam-oidc-provider --cluster workshop-cluster –approve

{

  “eksctl create iamserviceaccount \
  --name cloudwatch-agent \
  --namespace amazon-cloudwatch --cluster workshop-cluster \
  --role-name my-service-account-role \
  --attach-policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy \
  --role-only \
  --approve”
  
}

5.	Go to CloudWatch Container Insights, and you should see the metrics from the cluster flowing in! Explore the metrics

   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)

