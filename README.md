* Using-Kubernetes-to-deploy-a-WordPress-Site-with-MySQL-db-on-AWS *


This is a capstone project of my kubernetes self-paced learning Journey .

This is the architecture of the project !
![Capture d'écran 2024-08-25 153234](https://github.com/user-attachments/assets/4ddd1442-fc71-4855-a167-f2b4db9c7db7)

I will be working on kubernetes kubeadm cluster hosted on EC2 instances like in the photo, after making the right configuration to install kubeadm and join the nodes to master node we will obtain : 

![Capture d'écran 2024-08-25 180311](https://github.com/user-attachments/assets/37efbee3-a80a-40ec-a777-ab1e92f750b4)

![Capture d'écran 2024-08-25 185226](https://github.com/user-attachments/assets/ad0f344f-2871-4649-8b9c-aec856defec4)

First thing to do is Create a Role with permission to use EBS and EFS and attach it to EC2 instances 

Note : the EFS-policy is a custom policy (JSON) that i will leave in the Yaml-Config.yaml file : 

![Capture d'écran 2024-08-25 190048](https://github.com/user-attachments/assets/c5c4010d-9b40-41cd-8ffe-a971ae098628)


We will be using aws EFS for WordPress website since its a ReadWriteMany type of storage and I will be deploying the website on more than a Pod ,
also EBS for MySQl since it will be only on one Pod 
Most important thing to do is download the aws CSI on all nodes ( installing it on the master node is enough to be installed on joined worker nodes)

![Capture d'écran 2024-08-25 190631](https://github.com/user-attachments/assets/367fba38-646a-4798-ab3c-5bcf0206ae25)

![Capture d'écran 2024-08-25 190851](https://github.com/user-attachments/assets/3c198cea-0faf-46a2-a94f-dd3241e44623)

WordPress will be using the secret to acces mySQL . (creating a secret using base64)

![Uploading Capture d'écran 2024-08-25 154321.png…]()



Creating SC , PV , PVC , SVC , Deployements ,Secret , and EFS manually : 

![Capture d'écran 2024-08-25 154846](https://github.com/user-attachments/assets/bc2f61b8-f44d-4359-9809-4b1de8be8831)

![Capture d'écran 2024-08-25 194209](https://github.com/user-attachments/assets/94e5a7d8-56c1-4b30-adf3-2ae7872dec29)

![Capture d'écran 2024-08-25 195445](https://github.com/user-attachments/assets/426e7397-4647-43b1-bb4e-3770539cf6ed)

![Capture d'écran 2024-08-25 200750](https://github.com/user-attachments/assets/aae66760-bd8c-4be5-b622-f0e8001d2c86)

![Capture d'écran 2024-08-25 201056](https://github.com/user-attachments/assets/0a886ff0-f722-48e0-a5a0-f26e935f2737)


After creating deployements , pods will be created : 

![Capture d'écran 2024-08-25 200943](https://github.com/user-attachments/assets/cb120e61-583c-49eb-b40b-b685c55fbb07)

note that the service of the WordPress needs to be LoadBalancer Type so it can be attached to AWS ALB for better availabity 


EBS is created and its attached successfuly ! :

![Capture d'écran 2024-08-25 211323](https://github.com/user-attachments/assets/986bc311-2f73-4e8f-a3c6-2a47afeb9ac3)


Manually creating EFS : 

![Capture d'écran 2024-08-25 200511](https://github.com/user-attachments/assets/12b69b03-fd6f-41dc-922d-84e07ba68bc4)


"EBS will be created manually after the creation of the PVC , and EFS needs to be created manually so it will be attached to the PV of wordpress"

Using KubeView to monitor the cluster : 

![Capture d'écran 2024-08-25 201242](https://github.com/user-attachments/assets/65cbdf1f-a4e3-4470-b79c-44ddcdceebe0)

At this point we can acces our website using the WordPress LB svc's Port wich is 30216 and an EC2 public ip : 

![Capture d'écran 2024-08-25 201056](https://github.com/user-attachments/assets/b4f2b597-fad4-4626-b6a6-496f6c416348)

![Capture d'écran 2024-08-25 204525](https://github.com/user-attachments/assets/6c17c5b2-5a00-4665-af67-957a70c64b54)


Attaching an ALB for High Availability And Fault Tolerance:

First Thing to do is Creating a target group that groups all my Ec2 and wich forwards the traffic to the WordPress svc Port

![Capture d'écran 2024-08-25 204718](https://github.com/user-attachments/assets/58c8e5b7-7cb0-4eb0-9fcb-4272dbaa1c85)

 Then Creating ALB and linking it to the Target Group (Mapping the ALB to 2 AZ) : 
 
![Capture d'écran 2024-08-25 210304](https://github.com/user-attachments/assets/2d2eca12-ab44-48d1-b008-8c573f94f13f)

![Capture d'écran 2024-08-25 210633](https://github.com/user-attachments/assets/6123c7e6-a457-4c90-9890-a01d37d5f13e)

FINALLY ! accessing the WordPress with ALB dns name ! 

![image](https://github.com/user-attachments/assets/3a69d8d4-0be7-4097-b4d0-0c78e1375814)




 













