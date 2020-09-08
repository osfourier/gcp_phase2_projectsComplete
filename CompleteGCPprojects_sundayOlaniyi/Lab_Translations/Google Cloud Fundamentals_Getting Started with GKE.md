# LAB: Google Cloud Fundamentals: Getting Started with GKE

## OBJECTIVES:
In this lab, you learn how to perform the following tasks:
    - Provision a Kubernetes cluster using Kubernetes Engine.
    - Deploy and manage Docker containers using kubectl.

## STEPS:

1. Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend and configure it to run 2 nodes.
    - Export your zone for convenience;
            
        export MY_ZONE=us-central1-a

    - Now create the Kubernetes cluster;

        gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2

        gcloud container clusters get-credentials webfrontend --zone us-central1-c

    - check your installed version of Kubernetes;

        kubectl version

    - View your running nodes in the GCP Console
   
        kubectl get nodes

2. Launch a single instance of the nginx container using the cloud shell command line.
   
        kubectl create deploy nginx --image=nginx:1.17.10

    - View the pod running the nginx container
        kubectl get pods

3. Expose the nginx container to the Internet using the command line
   
        kubectl expose deployment nginx --port 80 --type LoadBalancer
    
    - View the new service,

        kubectl get services
    
    - Open a new web browser tab and paste your cluster's external IP address into the address bar to view the Nginx.

        kubectl get services
        
        curl https://<CLUSTER'S_EXTERNAL_IP>

        -Result: The default home page of the Nginx browser is displayed.

4. Scale up the number of pods running on your service'

        kubectl scale deployment nginx --replicas 3

    - Confirm that Kubernetes has updated the number of pods
  
        kubectl get pods

    - Confirm that your external IP address has not changed

        kubectl get sercives
    - Refresh the page to confirm that the nginx web server is still responding.

        curl https://{CLUSTER'S_EXTERNAL_IP}
        
        -Result: The default home page of the Nginx browser is displayed.

            
