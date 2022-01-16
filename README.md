# KUBERNETES-DOCS
        
        References: 
       > https://kubernetes.io/docs/home/
       > https://kubernetes.io/docs/concepts/overview/components/
       > https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/
       > https://kubernetes.io/docs/concepts/workloads/pods/
       > https://minikube.sigs.k8s.io/docs/start/
       > https://minikube.sigs.k8s.io/docs/handbook/
       > https://containerd.io/
       > https://kubernetes.io/docs/reference/kubectl/cheatsheet/
       


    Application Orchestrator
       - scales up according to demand
       - zero downtime deployments
       - rollbacks, etc...

    Cluster 
      - set of nodes 
      - Nodes-VM,Physical Machine running on aws, azure,google cloud etc.
      -MASTER NODE - all decisions are made
      -worker node - running applications etc. (Example- 1Master node - 3-Worker node)
    
   **MasterNode** 
   
      - Scheduler
            - watches for new workloads/pods and assigns them to a node based on several scheduling factors
            - checks nodes if healthy?, enough resouces? port available?
            -affinity and anti affinity rules etc
      - api server -for communication 
            - front end to kubernetes plane
            -All communications go through API server (both external and internal)
            - exposes port 443
            -authentication and authorization checks are performed
            //command kubectl apply -f <yaml>
      -cluster store
          - stores configuration and state of the entire application
          -etcd
          -Distributed key value data store
          -single sourde of truth
           //command kubectl apply -f <yaml> //yaml file goes all the way into cluster store
           https://etcd.io/
           https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/
          
      - controller manager
            - Deamon that manages control loop
            -controller of controllers
            -node controller
                if desired state =1 node, if node fails then node controller brings another node.
             - watches API server for changes and goal is to check if 
                desired state== current state else take action to make 
                desired state== current state
      -cloud controller manager 
        //kudectl apply -f ingress.yaml
        gives load balancer.
        creates load balancer based on underlying cloud controller provider (aws or azure or google cloud)
        
   **WORKER NODE**
   
          - provides running environment for our applications
          -3 Main components (kublet, container runtime and kube proxy)
          - KUBELET
              - Main agent that runs on every node
              - recieves pod definitions from api server
              - reports node and pod state to master
              - interacts with container runtime to run containers associated with the pod
          -CONTAINER RUNTIME
              -responsible for pulling images, starting and stopping containers
              -CRI (container Runtime Interface)
                - Interface for 3rd party container runtime
              -Containerd
                - https://containerd.io/
          -KUBE PROXY
              - agent runs on everyone through DeamonSets
              - Responsible for
                  - local cluster networking
                  -each node gets own unique IP address
                  -routing network traffic to load balanced services
 
 **RUNNING KUBERNETES**
           
           Managed Kubernetes (Master node fully managed by below services)
           - EKS - Elastic Kubernetes Service (from AWS)
           - GKE - Google Kubernetes Engine (from google cloud)
           - AKS - Azure Kubernetes (from Azure)
           -etc...
           
      -EKS 
          can be run using 
              - AWS forgate ( for serverless containers)
              - EC2 (for long run applictions)
          -Amazon EKS dashboard in the AWS console
  
  **RUNNING KUBERNETES LOCALLY**
  
     local cluster can be created using 
        - minikube
        - kubekind
        - docker
        Note: do not use it in any environment including production. used only for local development or CI
        
   **MINIKUBE**
   
      - Docker + Minikube
      - minikube quickly sets up a local kubernetes cluster on macOS
      
   **COMMANDS**
           
           % docker --version
           % docker login
           % docker run -d -p 80:80 docker/getting-started
           % docker ps
     Browser: http://localhost
           % docker stop <container id>
           % docker ps -a
           % docker rm -f <container id>
      
   **INSTALLING MINIKUBE AND MINIKUBE COMMANDS**
   
            - https://minikube.sigs.k8s.io/docs/start/
            % brew install minikube
            OR
            % curl -LO               https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
            sudo install minikube-darwin-amd64 /usr/local/bin/minikube
            % minikube version  
            % minikube start //to start a cluster
            % minikube status
            % docker ps //should list minikube container running on docker engine
            % minikube ip //ip address of master node
    
   **KUBECTL**
    
            - Kubernetes command line tool
            - runs commands against our cluster
               -deploy
               -inspect
               -edit resources
               -debug
               -view logs
               -etc
               
   **INSTALLING KUBECTL**
   
                - https://kubernetes.io/docs/tasks/tools/#kubectl
                - https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/
                %    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
                % chmod +x ./kubectl //make kubectl binary executable
                % sudo mv ./kubectl /usr/local/bin/kubectl  //move to a file location
                % sudo chown root: /usr/local/bin/kubectl //to make it root
                % kubectl version --client //for testing
    
   **KUBECTL- Creating POD** 
    
                %  docker run --rm -p 80:80 <kubernetes image>
                - http://localhost
                % CTRL+C
                % kubectl run <kubernetes image name> --image =<kubernetes image> --port=80 
                -//example 
                    - kubectl run hello-world --image=abhilashgd/kubernetes:hello-world --port=80
                % kubectl get pods // to list pods
                % kubectl port-forward pod/hello-world 8080:80
                % kubectl delete pod hello-world //to delete pod
                % kubectl get pods
    
   **Exploring cluster**
               
                % minikube status
                % kubectl get nodes
                % kubectl get pods
                % kubectl get pods -A
                % kubectl run hello-world --image=abhilashgd/kubernetes:hello-world --port=80
                % kubectl get pods
                % kubectl get pods -A
                
   **SSH into NODES**
    
                % kubectl get nodes
                % minikube ssh --help
                % minikube ssh //takes into minikube container
                % cd ../..
                % cd /
                % pwd
                % ls //shows linux file structure
                % cd bin
                % ls
                % cd /
                % docker --version //docker version inside minikube
                % docker ps //containers inside minikube
               
   **Starting and Stopping Cluster**
                
                % minikube status
                % minikube stop
                % minikube status
                % minikube start
                % minikube status
                % minikube delete //to delete entire cluster
                
   **Cluster with 2 nodes**
            
                % minikube start --help
                % minikube start --nodes=2
                % minikube status
                % kubectl get nodes
                % minikube ip
                % minikube ip --node minikube
                % minikube ip --node minikube-m02
   
   **MINIKUBE LOGS**  
              
                % minikube logs 
                % minikube logs -f
                % kubectl get nodes
                % minikube logs --help
                % minikube logs --node=minikube-m02 -f
                
   **PODS**
    
                --> Group of 1 or more container
                --> Represents a running process
                --> Share network and volumes
                --> Never create pods on its own. Use Controllers instead
                --> Ephemeral and disposable
                --> smallest deployable unit 
                
                //Creating pods
                --> //Imperative command --for troubleshooting, experimenting
                % kubectl run hello-world --image=abhilashgd/kubernetes:hello-world --port=80
                -->//Declarative command -- for environments -reproducible
                //using a configuration file
                pod.yml
                
                //Command
                % kubectl get pods
                % kubectl run hello-world --image=abhilashgd/kubernetes:hello-world --port=80
                % kubectl get pods
                % kubectl port-forward pod/hello-world 8080:80 //port forwarding only for testing purpose
                //iterm shortcut - ctrl+r for searching previous commands
                
                //Creating pod usig configuration
                pod.yml //under code
                % kubectl get pods
                % kubectl delete pod hello-world
                % kubectl get pods
                % cat pod.yml
                % kubectl apply -f pod.yml
                % kubectl get pods
                % kubectl port-forward pod/hello-world 8080:80 //for testing
                
 **VS CODE Extensions**
 
                - Kubernetes
                - indent-rainbow
                - code formatter - prettier
                - docker
                - bridge to kubernetes
                - vim
                - YAML
                
**CREATE, LIST, DESCRIBE and DELETE Resources**

                % kubectl get pods
                % kubectl apply -f pod.yml //create best practice
                or
                % cat pod.yml | kubectl apply -f - 
                % kubectl get pods
                % kubectl delete pod hello-world
                % kubectl apply -f .
                % kubectl get pods 
                % kubectl delete -f pod.yml //Delete best practice
                % kubectl get pods -w
                % kubectl get pods 
                
                //List Resources
                % kubectl get pods -A
                % kubectl get all
                % kubectl get all -A
                % kubectl get pod -n kube-system
                % kubectl get all -n kube-system
                % kubectl get namespaces
                 or
                % kubectl get ns
                
                //Kubectl Describe
                % kubectl describe pod hello-world
                % kubectl get pod hello-world --output wide
                % kubectl get pod hello-world -o yaml
                % kubectl get pod hello-world -o json
  
 **Debugging with KUBECTL -Logs**
                
                % kubectl get pods
                % kubectl logs hello-world
                % kubectl logs hello-world -f //tailing logs
                % kubectl describe pod hello-world  
                % kubectl logs hello-world -c hello-world
                % code pod.yml //opens VSCode
                % kubectl delete pod -f pod.yml
                % kubectl apply -f pod.yml
                % kubectl logs hello-world -c hello-world
                
**Shell Access to a Running pod**                

                % kubectl exec -it hello-world -c hello-world  -- bash
                or 
                % kubectl exec -it hello-world -c hello-world  -- /bin/bash
                % kubectl exec -it hello-world -c hello-world  -- sh
                or
                % kubectl exec -it hello-world -c hello-world -- /bin/sh
                //to execute just one command
                % kubectl exec hello-world -- ls /
                % kubectl exec hello-world -- ps aux 
                
                //accessing via port forward
                % kubectl port-forward pod/hello-world 8080:80
                % kubectl describe pod hello-world
                % kubectl port-forward pod/hello-world 8081:80
                
                //to list all type of resources
                % kubectl api-resources
                          //above command lists aa resources including
                           - secrets, pods, nodes,sservices, deployments, deamonsets
                % kubectl get po
                % kubectl get no
                
**KUBECTL cheat sheet**

                % kubectl --help
                % kubectl get --help
                // logs, exec, get, port-forward, delete, run, 
                https://kubernetes.io/docs/reference/kubectl/cheatsheet/
                
                
                --> never diploy using kind:pod
                --> they are ephemeral
                --> pods dont self heal //once we delete, pod wont come up by itself
                
**DEPLOYMENTS**

                -- manage pods through deployments
                -- manages release of new application
                -- zero downtime deployments
                -- creates replicateSet //ensures desired number of pods running
                
**CREATING DEPLOYMENTS**

                -- VS Code //deployment.yaml
                % kubectl delete pod hello-world
                % kubectl get pods
                % kubectl apply -f deployment.yaml
                % kubectl get pods
                
**MANAGING DEPLOYMENTS**   
  
                % kubectl get deployments
                % kubectl describe deployment hello-world
                % kubectl delete deployment hello-world
                % kubectl get po
                % kubectl apply -f deployment.yaml
                % kubectl get po
                % kubectl delete -f deployment.yaml
                % kubectl get po
                % kubectl apply -f deployment.yaml
                
 **REPLICASET**
 
                - Ensures desired number of pods always running
                //CONTROL LOOPS
                        - replicaset implement a background control loop that checks the desired number of pods are always on the cluster
                //LISTING REPLICATE SETS
                
                % kubectl api-resources 
                //always use deployments. never create replicate sets on its own
                % kubectl get replicaset
                or
                % kubectl get rs
                % kubectl describe rs hello-world-65f9744cf8
                % kubectl get po
                //never delete replicaset. delete deployment instead which manages replicaset
                //PORT FORWARD DEPLOYMENTS
                % kubectl get deployment
                % kubectl get po
                % kubectl port-forward deployment/hello-world 8081:80
                http://localhost:8081/
                % kubectl scale --replicas=3 -f deployment.yaml //scaling up replicas
                % kubectl get po
                % kubectl apply -f deployment.yaml
                % kubectl get deployment
                % kubectl describe rs hello-world
                
**Kubernetes Services**

                - service will have stable IP address, stable DNS Name, stable Port
                //Types
                - ClusterIP (Default)
                - NodePort
                - ExternalName
                - LoadBalancer
              
**customer Microservice Deployment**

                //customer-deployment.yaml
                % kubectl apply -f customer-deployment.yaml
                % kubectl apply -f deployment.yaml //changed scale to 2
                % kubectl get pods
                % kubectl logs customer-7758669b95-5vfzn
                % kubectl get all
                % kubectl get deploy
                % kubectl port-forward deployment/customer 8080:8080 //for debugging purposes
                //order.yaml
                % kubectl apply -f order-deployment.yaml
                % kubectl get pods -w
                % kubectl logs order-7d87cb7758-4mpht
                
 **Microservice Communication using POD IP address**
 
                //WRONG WAY
                put value for ORDER_SERVICE in customer-deployment.yaml as ip address
                fetched from 
                % kubectl describe pod order-7d87cb7758-z4z9c
                % kubectl apply -f customer-deployment.yaml
                % kubectl get pods
                % kubectl port-forward deployment/customer 8080:8080
                
                //RIGHT WAY
                Service -
                
                order-deployment.yaml
                //added service
                % kubectl apply -f order-deployment.yaml
                % kubectl get service
                % kubectl describe service order
                //end points are the list of healthy pods 
                % kubectl get pods
                % kubectl describe pod order-7d87cb7758-z4z9c
                % kubectl get endpoints
                //put service IP address in customer-deployment.yaml ORDER_SERVICE value
                % kubectl apply -f order-deployment.yaml
                % kubectl apply -f customer-deployment.yaml
                % kubectl get deploy
                % kubectl port-forward deployment/customer 8080:8080
                http://localhost:8080/api/v1/customer/2/orders
                http://localhost:8080/api/v1/customer/1/orders
                http://localhost:8080/api/v1/customer/3/orders
                % kubectl get service
                
# Node Port Service Type

                - allows you open a port on all nodes
                - Port range 30000-32767
                - client sends request directly to node IP address:port
                                example: 192.168.49.2:30001
                % kubectl apply -f customer-deployment.yaml
                % kubectl get pods
                % kubectl get service OR kubectl get svc
                % kubectl describe svc customer-node
                
# Accessing API with NodePort Service

                % docker ps
                % kubectl get nodes
                % minikube ip
                % minikube ip -n minikube-m02
                % minikube ssh
                $ curl localhost:30000/api/v1/customer
                $ curl 192.168.49.3:30000/api/v1/customer //192.168.49.3 is minikube-02 ip
                $ exit
                
                //in another terminal
                % minikube ssh -n minikube-m02
                $ curl localhost:30000/api/v1/customer
                $  curl 192.168.49.2:30000/api/v1/customer //192.168.49.3 is minikube node1 IP
                $ exit
                % kubectl get svc
                % minikube service customer-node --url
                🏃  Starting tunnel for service customer-node.
                |-----------|---------------|-------------|------------------------|
                | NAMESPACE |     NAME      | TARGET PORT |          URL           |
                |-----------|---------------|-------------|------------------------|
                | default   | customer-node |             | http://127.0.0.1:55833 |
                |-----------|---------------|-------------|------------------------|
                http://127.0.0.1:55833
                
                BROWSER: http://127.0.0.1:55833/api/v1/customer  //accessing service direclty
                BROWSER: http://127.0.0.1:55833/api/v1/customer/1/orders
                
# NODE PORT with RANDOM PORT
                
                //if we do not mention nodePort in service under customer-deployment.yaml, it allocates random port
                //delete nodePort in customer-deployment.yaml
                % kubectl apply -f customer-deployment.yaml
                % kubectl get svc
                % minikube ip -n minikube-m02
                % minikube ssh 
                % exit
                % kubectl get nodes
                % minikube ssh
                $ curl localhost:30675/api/v1/customer //30675 randomly generated port
                $ curl 192.168.49.3:300675/api/v1/customer //accessing 2nd node
                $ exit
                % kubectl get svc
                % minikube service customer-node
                BROWSER: http://127.0.0.1:51345/api/v1/customer 
                BROWSER: http://127.0.0.1:51345/api/v1/customer/1/orders
                
                % kubectl delete svc customer-node
                % kubectl apply -f customer-deployment.yaml //changed customer-node to customer in service
                % kubectl get svc
                
# Accessing NodePort Service Using Cluster IP Address
        
                % kubectl get svc
                % kubectl get pods
                % kubectl exec -it order-7d87cb7758-5p9ts -- sh
                # apk add curl
                # curl
                # curl 10.106.250.236/api/v1/customer
                # curl customer/api/v1/customer
                # exit
                % kubectl get svc
                % kubectl delete svc customer
                % kubectl apply -f customer-deployment.yaml //changed back customer to customer-node in service
                % kubectl get svc
                
# LOAD BALANCER SERVICE
        
                - standard way of exposing applications to the internet
                - creates a load balancer per service
                -AWS and GCP - Network Load Balancer NLB
                - MINIKUBE - minikube tunnel
               
                //GOOGLE CLOUD CONSOLE -               https://console.cloud.google.com/marketplace/product/google/compute.googleapis.com?returnUrl=%2Fnet-services%2Floadbalancing%2Flist%2FloadBalancers%3F_ga%3D2.66755886.1291078104.1642311903-1084078483.1642311903%26_gac%3D1.48331476.1642311903.CjwKCAiA_omPBhBBEiwAcg7smZkKOtxPESBumDCMgN1MXeQh5DQOszy4g-Ygy_xSYRes0sxJ2KjTyhoCjJcQAvD_BwE%26pli%3D1%26project%3Demerald-result-174814&project=emerald-result-174814
                
                https://cloud.google.com/load-balancing/?utm_source=google&utm_medium=cpc&utm_campaign=japac-IN-all-en-dr-bkws-all-pkws-trial-b-dr-1009882&utm_content=text-ad-none-none-DEV_c-CRE_468709820526-ADGP_Hybrid%20%7C%20BKWS%20-%20PHR%20%7C%20Txt%20~%20Networking%20~%20Cloud%20Load%20Balancing_cloud%20load%20balancing-general%20-%20Products-KWID_43700065772992381-kwd-351186651341&userloc_9062011-network_g&utm_term=KW_google%20cloud%20load%20balancing&gclid=CjwKCAiA_omPBhBBEiwAcg7smZkKOtxPESBumDCMgN1MXeQh5DQOszy4g-Ygy_xSYRes0sxJ2KjTyhoCjJcQAvD_BwE&gclsrc=aw.ds
                
                
# CLOUD CONTROLLER MANAGER
 
                - AWS
                - Azure
                - Google cloud

# FULL STACK APP EXPOSED WITH LOAD BALANCER SERVICE

                % kubectl get pods
                % kubectl apply -f customer-deployment.yaml
                % kubectl get svc
                % kubectl apply -f frontend.yaml
                % kubectl get po
                % kubectl get svc
                % kubectl get svc -w
                // in another terminal
                % minikube tunnel
                BROWSER: http://127.0.0.1/
                
# KUBERNETES DEFAULT SERVICE

                % kubectl get svc
                % kubectl get ep //to get end points
                % kubectl get pod -A
                % kubectl describe pod kube-apiserver-minikube -n kube-system
                % kubectl get svc
                
                
