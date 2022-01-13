# KUBERNETES-DOCS

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
                pod.yml
                
