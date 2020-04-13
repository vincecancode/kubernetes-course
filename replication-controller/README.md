#Replication Controller


## Create replicas 
Create the pod with 2 replicas 
    
    kubectl apply -f replication-controller/helloworld-repl-controller.yml

    kubectl get pods

It shows 2 pods running.

## Delete pod
If delete one pod 

    kubectl delete pod <pod_name>
    
The status will turn terminating, and another pod start running 

## Scale
To scale the number of pods

    kubectl scale --replicas=4 -f replication-controller/helloworld-repl-controller.yml



