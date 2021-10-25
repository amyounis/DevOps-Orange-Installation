# DevOps-Orange-Installation
GitHub Repository for DevOps Orange Assignment Installation
###### 1.	Install local k8s instance using Minikube

###### 1.1 Prerequisites

        - Docker
        - VirtualBox
        - Kubectl 

###### 1.2 Installation steps

        -	Run the following commands to install minikube, you can specify which version to install by replace it in the URL, example installs minikube v0.27.0
  
      ```
      curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.27.0/minikube-darwin-amd64 &&\
      chmod +x minikube &&\
      sudo mv minikube /usr/local/bin/
      sudo install minikube-darwin-amd64 /usr/local/bin/minikube
      ```
       - Once installation done you can start minikube Kubernetes cluster using command minikube start as shown below. Aslo you can run any kubectl commands 

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136691939-694d22c3-e12a-45c8-8b4e-b23d83b42452.png">

      - You can check the status of minikube container status using docker desktop 
 
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136692443-6b57641e-cb06-40cd-9c4b-4f1d6a891afe.png">

      - Problems may counter while using minikube 

          - “TLS handshake timeout”, “request canceled (Client.Timeout exceeded while awaiting headers)”
sometimes container becomes not healthy or reachable that causes return this message

              - You can check the status of minikube using command minikube status and restart it if it is not healthy or stopped using command minikube stop then minikube start

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693062-95500c38-7839-4dc8-8bfa-3bab1092b799.png">
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693079-8c55104a-83f5-42fc-9289-17bc7cdfedfa.png">

 
              - You can check the minikube container CPU or Memory using Docker Desktop and if exceeds you can increase them as shown below

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693094-3b3cd993-4663-45af-98d4-b754b0d706d9.png">

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693133-760df648-be70-4fce-b062-9f58087500fb.png">

 
 
###### 2.	Install Jenkins and Nexus Repository OSS
######    2.1 Prerequisites
            -	Helm 

######    2.2 Jenkins Installation

            - We need to install Jenkins on pod container in build namespace on minkube cluster 
            - I have used Helm charts for installation

            - Run helm repo add command to add the Github repository for Jenkins helm charts 
              'helm repo add jenkins https://charts.jenkins.io'
              'helm repo update'
              
            - Run command Kubens build to install helm charts on build namespace
            - To run Jenkins on a pod as stateful app and not lose the Jenkin data when pod restart, you need to create K8s persistence volume, you can find the k8s manifest yaml file in github path https://github.com/Eman-Github/DevOps-Orange-Installation/blob/main/jenkins/jenkins-pv.yaml
               Using command 'kubectl apply -f jenkins-pv.yaml'

            - To give privilege to install Jenkins on  K8s you have create service account I have added Jenkins-sa.yaml in github path https://github.com/Eman-Github/DevOps-Orange-Installation/blob/main/jenkins/jenkins-sa.yaml
               Using command 'kubectl apply -f jenkins-sa.yaml'

            - Customize the Helm chart values file, I have added Jenkins-values.yaml in github path https://github.com/Eman-Github/DevOps-Orange-Installation/blob/main/jenkins/jenkins-values.yaml
            - Install Helm charts for Jenkins on minikube k8s cluster
                'helm install jenkins -f jenkins-values.yaml  jenkinsci/jenkins'
                'helm list'

            - Check that the pods for Jenkins are running 

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693311-23a6903f-8901-442b-9129-3cf7b9da49f3.png">

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693321-a16d9690-6ff1-44cc-888d-d0c1e57ac7f0.png">
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693322-4ddd2ee1-3783-421d-bbaf-6ca75f8bfa00.png">

###### 2.3 Nexus Installation

        •	We need to install Nexus on pod container in build namespace on minkube cluster 
        •	I have used Helm charts for installation
        •	Prerequisites
          o	Helm 
        •	Installation steps 
          o	Run helm repo add command to add the Github repository for Nexus helm charts 
              'helm repo add oteemocharts https://oteemo.github.io/charts'
          o	To run Jenkins on a pod as stateful app and not lose the Jenkin data when pod restart, you need to create K8s persistence volume, you can find the k8s manifest yaml file in github path https://github.com/Eman-Github/DevOps-Orange-Installation/blob/main/nexus/nexus-pv.yaml
                Using command 'kubectl apply -f nexus-pv.yaml'

          o	To give privilege to install Jenkins on  K8s you have create service account I have added nexus-sa.yaml in github https://github.com/Eman-Github/DevOps-Orange-Installation/blob/main/nexus/nexus-sa.yaml
                Using command kubectl apply -f nexus-sa.yaml

          o	Customize the Helm chart values file, I have added Jenkins-values.yaml in github path https://github.com/Eman-Github/DevOps-Orange-Installation   /blob/main/nexus/nexus-values.yaml
              Install Helm charts for Nexus on minikube k8s cluster
              helm install nexus -f nexus-values.yaml oteemocharts/sonatype-nexus
              helm list

          o	Check that the pods for Nexus are running 

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693370-96bbc99b-781b-4499-8738-a0e9844d6754.png">
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693379-5d10965e-6a63-4b2e-8e48-fe255bcc454d.png">


###### 2.4	Ansible to create Kubernetes namespaces, deploy Jenkins and Nexus

        - Created ansible folder on Github path
 https://github.com/Eman-Github/DevOps-Orange-Installation/tree/main/ansible
 
        - The ansible folder contains folders for Jenkins and Nexus installation files
        - In Jenkins folder added the ansible yaml file (jenkins-ansible-install.yaml) which do :
              o	Create the Jenkins helm chart perquisite resources (Persistence Volume and Service Account) 
              o	Use ansible module kubernetes.core.helm to install Jenkins helm chart

         - In nexus folder added the ansible yaml file (nexus-ansible-install.yaml) which do :
              o	Create the nexus helm chart perquisite resources (Persistence Volume and Service Account) 
              o	Use ansible module kubernetes.core.helm to install nexus helm chart

          -	To run Ansible playbooks, you need to install ansible depend on your system , follow instruction on this page https://docs.ansible.com/ansible/latest /installation_guide/intro_installation.html

          -	Run ansible-playbook command for both Jenkins and nexus using command 
                o	ansible-playbook ./jenkins-ansible-install.yaml
                o	ansible-playbook ./nexus-ansible-install.yaml
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693471-e56d6ade-70c4-41e2-ab81-15220b99648a.png">
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693479-6f453e7a-45ec-497a-af14-eff9e0d30d56.png">

######  3.	Spring Boot Application (ToyStore) Dockerfile
      Dockerfile pushed to GitHub repo URL https://github.com/Eman-Github/My-Devops-Orange/blob/main/Toy0Store/Dockerfile
        ```
        # create image from base image openjdk which a linux and has Java
        FROM openjdk:8-jre-alpine
        # copy jar file into image
        COPY target/Toy0Store-1.0.jar /Toy0Store-1.0.jar 
        EXPOSE 8000
        # run application with this command line 
        CMD ["/usr/bin/java", "-jar", "-Dspring.profiles.active=default", "/Toy0Store-1.0.jar"]
        ```

 ###### 4.	MySql DB Dockerfile
      Dockerfile pushed to GitHub repo URL https://github.com/Eman-Github/My-Devops-Orange/blob/main/Database/Dockerfile
        ```
        # create image from base image mysql
        FROM mysql:5.6
        # add environment variable of the DB name to the container
        ENV MYSQL_DATABASE=toystore
        # copy all *.sql files and docker-entrypoint-initdb.d/ to container to be run when container start
        COPY ./*.sql /docker-entrypoint-initdb.d/
        ```
 
 ######  5.	Jenkins Pipeline for ToyStore App 
         Jenkinsfile pushed to GitHub rep URL https://github.com/Eman-Github/DevOps-Orange-Installation/blob/main/Jenkinsfile

   ######   5.1 Pipeline Creation Steps

        -	Open Jenkins URL and click Dashboard --> New Item

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693680-3d23cda2-ad9b-4823-87ee-9fbbd1104c5b.png">

        -	Open the created Pipeline and click Configure 
        
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693697-8e1fe62a-f8c7-4061-a49d-f5736b0ffdcf.png">

        -	In General tab add the description and check This project is parameterized so the user can choose which tag code to be deployed, add the GitHub repo to clone and Parameter Type is Tag to list Tags on Github repos.

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693718-22b6d6a0-41b3-4d0d-8ebd-a0fbfa1587a2.png">
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693723-3f435c26-c2ba-46f4-bf76-ad87f5939e7c.png">

        -	So, when you start new Build With Parameters, It displays tags to be selected as below

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693736-e583463d-2045-4b91-b77a-158c4c852b90.png">

        -	In configure Pipeline tab, add the Jenkins pipeline script.
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693751-acdf1be6-ab29-4bf3-8ac6-29d0703d8e35.png">

######  6.	Deploying Toystore app on Minikube cluster using Helm and Ansible

        -	Created helm chart for toystore app on Github path
            https://github.com/Eman-Github/DevOps-Orange-Installation/tree/main/kubernetes/charts/toystore

        -	The toystore folder contains 
            o	Chart.yaml
            o	Templates folder for deployment (deployment.yaml) and service (service.yaml) templates 
            o	Customized values.yaml file for each dev (dev-values.yaml) and test (test-values.yaml)
            o	Ansible yaml files for both dev (toystore-ansible-install-dev.yaml) and test (toystore-ansible-install-test.yaml)

        -	To run Ansible playbooks, you need to install ansible depend on your system , follow instruction on this page https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
        -	Command to run ansible playbook on dev namespace is ansible-playbook ./toystore-ansible-install-dev.yaml

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693796-33f4f5d9-e2aa-483a-9239-e9d49c7884a9.png">

        -	Command to run ansible playbook on test namespace is ansible-playbook ./toystore-ansible-install-test.yaml
        
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693812-350d2951-c7f6-42de-ac51-c791749fe5d4.png">


 ###### 7.	Automate deploying Toystore app on Minikube cluster using Jenkins

        -	Install Ansible plugins on Jenkins using Jenkins Plugins Manager 
          Manage Jenkins -- > Manage Plugins, search for Ansible in Available tab then install.

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693916-f72809fa-febf-431e-9dd7-87270c0ba688.png">

        -	Configure Ansible installations on Jenkins using Global Tool Configuration , set the Ansible executables path. You can get the executables path from run command ansible –version

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693936-471055c7-9fc5-4f8a-90b3-af2c60d9b935.png">

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693940-1278fd22-694e-4dcf-9190-a3e6c3bcd5e0.png">

        -	In Jenkins Create New Item  FreeStyle Project , then click configure on left side 
        -	In General tab, check This project is parameterized to add 2 parameters to jenkins build
              o	Choice Parameter (ENVIRONMENT), to allow user choose between which environment to deploy toystore (dev or test)
              o	String Parameter (TAGS) to allow user to add which tag version to deploy

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693960-a152df69-f921-46ab-b95f-f47fc18673e2.png">

        -	In Source Code Management tab, select Git to add the repository URL for the code to clone in Jenkins build.
        
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136693980-010b66dd-0641-4785-822f-4f636d338853.png">

        -	In Build tab, Add build step -- > Execute shell

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136694003-a62d8f48-54a1-459e-bac4-ca81cecfac2e.png">

        -	In Execute shell add the following bash script, which do 
            o	Get the build parameters for Environment and Tag 
            o	Create temp.yaml file for adding the tag value of the image to be pulled, temp.yaml file already added in the ansible configuration yaml (toystore-ansible-install-dev.yaml) file in under values_files
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136694021-84dbcc8d-d623-4637-8af7-90f7d49c0733.png">

        -	Run Jenkins Build with Parameters, select which environment to deploy in and the app tag version.
        
<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136694039-911ebeaf-755f-4275-b652-d404175b9d2f.png">

        -	Check Jenkins build Console Output to see logs 

<img width="468" alt="image" src="https://user-images.githubusercontent.com/48209733/136694071-a7cec46a-1b94-4885-b4c9-04402f3752fb.png">

  ######  8.	GitHub links 

  https://github.com/Eman-Github/DevOps-Orange-Installation

  https://github.com/Eman-Github/My-Devops-Orange















