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
    
    MasterNode 
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
      
