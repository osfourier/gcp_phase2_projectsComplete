# LAB: Virtual Private Networks (VPN)

## OBLECTIVES:
In this lab, you learn how to perform the following tasks:

    - Create VPN gateways in each network

    - Create VPN tunnels between the gateways

    - Verify VPN connectivity
 
## STEPS:

1.  Explore the networks and instances
   
   a. Verify that vpn-network-1 and vpn-network-2 have been created with subnets in separate regions using the cloud console.

            gcloud compute networks list
            gcloud compute networks subnets list
    b. Explore the firewall rules;

            gcloud compute firewall-rules list
    
    c. From the cloud console explore the instances and their connectivity 

        - Note the external and internal IP addresses for server-2. Get the servers IP addresses using the command below;

            gcloud compute instances list
        
     - For server-1, click SSH to launch a terminal and connect to test connectivity to server-2's external IP address

            gcloud compute ssh server-1
            ping -c 3 <Enter server-2's external IP address here>
                
            ## RESULT: This works because the VM instances can communicate over the internet.

        - To test connectivity to server-2's internal IP address

            ping -c 3 <Enter server-2's internal IP address here>

            ## RESULT: 100% packet loss when pinging the internal IP address because you don't have VPN connectivity yet.

            exit

     - Let's try the same from server-2. Note the external and internal IP addresses for server-1 and SSH to Server-2

            gcloud compute instances list
            gcloud compute ssh server-2

        - To test connectivity to server-1's external IP address
  
            ping -c 3 <Enter server-1's external IP address here>

        - To test connectivity to server-1's internal IP address;
  
            ping -c 3 <Enter server-1's internal IP address here>

            ##RESULT: You should see similar results.

            exit

2. Create the VPN gateways and tunnels.
    a. Using cloud console reserve two static IP addresses using the specified parameters;

     -  Reserve static address for "vpn-1-static-ip" in "us-central1" Region.

            gcloud compute addresses create vpn-1-static-ip --region us-central1
        
     -  Reserve static address for "vpn-2-static-ip" in "europe-west1" Region.

            gcloud compute addresses create vpn-2-static-ip --region europe-west1

     - NOte the reserved static IPV4 addressess for "vpn-1-static-ip" and "vpn-2-static-ip"
  
            gcloud compute addresses list

    b. Create the vpn-1 gateway and tunnel1to2 running the following commands and using the specified parameters.

            gcloud compute  target-vpn-gateways create "vpn-1" --region "us-central1" --network "vpn-network-1"

            gcloud compute forwarding-rules create "vpn-1-rule-esp" --region "us-central1" --address "[vpn-1-static-ip]" --ip-protocol "ESP" --target-vpn-gateway "vpn-1"

            gcloud compute forwarding-rules create "vpn-1-rule-udp500" --region "us-central1" --address "[vpn-1-static-ip]" --ip-protocol "UDP" --ports "500" --target-vpn-gateway "vpn-1"

            gcloud compute forwarding-rules create "vpn-1-rule-udp4500" --region "us-central1" --address "[vpn-1-static-ip]" --ip-protocol "UDP" --ports "4500" --target-vpn-gateway "vpn-1"

            gcloud compute vpn-tunnels create "tunnel1to2" --region "us-central1" --peer-address "[vpn-2-static-ip]" --shared-secret "gcprocks" --ike-version "2" --local-traffic-selector "0.0.0.0/0" --target-vpn-gateway "vpn-1"

            gcloud compute routes create "tunnel1to2-route-1" --network "vpn-network-1" --next-hop-vpn-tunnel "tunnel1to2" --next-hop-vpn-tunnel-region "us-central1" --destination-range "10.1.3.0/24"
    
    c. Create the vpn-2 gateway and tunnel2to1 running the following commands and using the specified parameters.

            gcloud compute target-vpn-gateways create "vpn-2" --region "europe-west1" --network "vpn-network-2"

            gcloud compute forwarding-rules create "vpn-2-rule-esp" --region "europe-west1" --address "[vpn-2-static-ip]" --ip-protocol "ESP" --target-vpn-gateway "vpn-2"

            gcloud compute forwarding-rules create "vpn-2-rule-udp500" --region "europe-west1" --address "[vpn-2-static-ip]" --ip-protocol "UDP" --ports "500" --target-vpn-gateway "vpn-2"

            gcloud compute forwarding-rules create "vpn-2-rule-udp4500" --region "europe-west1" --address "[vpn-2-static-ip]" --ip-protocol "UDP" --ports "4500" --target-vpn-gateway "vpn-2"

            gcloud compute vpn-tunnels create "tunnel2to1" --region "europe-west1" --peer-address "[vpn-1-static-ip]" --shared-secret "gcprocks" --ike-version "2" --local-traffic-selector "0.0.0.0/0" --target-vpn-gateway "vpn-2"

            gcloud compute routes create "tunnel2to1-route-1" --network "vpn-network-2" --next-hop-vpn-tunnel "tunnel2to1" --next-hop-vpn-tunnel-region "europe-west1" --destination-range "10.5.4.0/24
        
3. Verify VPN connectivity
   
        gcloud compute target-vpn-gateways list
        gcloud compute vpn-tunnels list
    
 - Verify the servers connectivity.
  
    -For server-1, click SSH to launch a terminal and connect then ping server-2's internal IP address;
        
        ping -c 3 <insert server-2's internal IP address here>
        exit
    
    -For server-2, click SSH to launch a terminal and connect then ping server-1's internal IP address;

        ping -c 3 <insert server-1's internal IP address here>
        exit
    
 - Remove the external IP addresses.
    -Select the server-1 instance and click Stop. Wait for the instance to stop. 

        gcloud compute instances stop server-1 --zone us-central1-b
    
    - Edit and change External IP to None.

        gcloud compute instances describe server-1 --zone us-central1-b
        gcloud compute instances delete-access-config server-1 --access-config-name "External NAT"

    - Start Server-1 again using the command below;
  
        gcloud compute instances stop server-1 --zone us-central1-b

    - SSH to server-2 and verify that you can still ping the server-1 instance's internal IP address.
        
        gcloud compute ssh server-2

        ping -c 3 <insert server-1's internal IP address here>
        exit

        ## RESULT: This should work as expected.