# EKS Workshop

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/68fc0e4337dd7e65edc9c4e4adc091485dd39a44/assets/K8-Arch.png)

## Enhanced Observability Configuration:
#### Lets add AWS CloudWatch Observability as an "add-on" to enable container sights within the cluster by following the below steps.

STEP 1.	Create IAM role.


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

        

STEP 2.	IDE and EKS Cluster SetUp
        Run https://catalog.workshops.aws/karpenter/en-US/workshop-setup#in-your-own-account
  	
STEP 3.	Open IDE (Get the URL and user credentials from Cloudformation Stack Outputs section)

STEP 4.  Create folder and subfolder and copy all related files like detailed-resume.html,Dockerfile,headshot.jpg,loadbalancerservice.yaml

STEP 5.	Then run command: cd awscourseforbeginners/resumefromkubernetes

STEP 6.	Run command: "docker build -t custom-httpd ."

STEP 7.	Run command: "docker images" - Copy the image-id of the custom-httpd image and keep in a notepad

STEP 8.	Create a free userid password at https://hub.docker.com/

STEP 9.	Create a repository in dockerhub with name custom-httpd
     NOTE: Docker repository Visibility must be set to Public, otherwise docker pull image will fail as there is no docker secret.
     
STEP 10.	Go back to IDE, login to dockerhub from CLI by running command : docker login --username=<GIVE DOCKERHUB USERNAME> . At prompt give password

STEP 11. Tag your container image by running command: docker tag <IMAGE_ID OF THE custom-httpd> <DOCKERHUB USERID>/custom-httpd

STEP 12. Push your container image by running command: docker push > <DOCKERHUB USERID>/custom-httpd

STEP 13. Open loadbalancerservice.yaml, and under containers, replace “image: httpd” with “image: <DOCKERHUB USERID>/custom-httpd” .Save the file 

STEP 14.	Apply this manifest file by running command : kubectl apply -f loadbalancerservice.yaml

STEP 15.	After 5 mins loadbalancer should be up and running. Get loadbalancer URL by running command: kubectl get service

STEP 16.	Copy the loadbalancer URL (Under EXTERNAL-IP column), and open it in a webpage

STEP 17.	Update the html, and include ur pic in headshot.jpg and re-run from building docker image (Step1)


## How to configure observability


![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Observability.png)


STEP 1. Go to EKS console and select your cluster

STEP 2.	If you see this message on top, click “Create access entry”, and go through the steps, choose EKSadmin policy from drop down,then click Add-Ons tab ,click “Get more add-ons”

STEP 3.	Scroll down and select “Amazon CloudWatch Observability” , Scroll down further and click “Next”

STEP 4.	We need to create the IRSA for the Cloudwatch Agent
   a.	Click “CloudWatch Observability add-on User Guide”
   https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Observability-EKS-addon.html
   
   b.	Run steps 1 and 2 in this page   
   https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Observability-EKS-addon.html#install-CloudWatch-Observability-EKS-addon-serviceaccountrole 
       
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


STEP 5.	Go to CloudWatch Container Insights, and you should see the metrics from the cluster flowing in! Explore the metrics

#### Control-plane Monitoring:

   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Controlplane.png)


#### workshop cluster Info:
   
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Clusteroverview.png)


#### Container Insights - Map View:
   
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20mapview.png)


#### Load balancers Details:
   
   ![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Loadbalancer.png)


   

#### Container Insights - Clusters:

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20Insights-Clusters.png)

#### Container Insights - Namespaces:

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20Insights-Namespaces.png)


#### Container Insights - Nodes:

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20Insights-Nodes.png)


#### Container Insights - Services:

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20Insights-Services.png)


#### Container Insights - Workloads:

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20Insights-Workloads.png)


#### Container Insights - Pods:

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20Insights-Pods.png)


#### Container Insights - Containers:

![img](https://github.com/Awadheshks/EKS-Hands-On/blob/bdc215433d13648c9865e7151cf64cf4d7e56e86/assets/Container%20Insights-Containers.png)   

