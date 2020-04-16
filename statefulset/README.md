# StatefulSet

https://www.udemy.com/course/learn-devops-the-complete-kubernetes-course/learn/lecture/10616128#overview


- For stateful applications which need a stable pod hostname

- Podname will have a sticky identity, using an index, e.g. podname-0, podname-1, 
podname-2 (and when a pod get rescheduled, it'll keep that identity)

- StatefulSets allow stateful app stable storage with volumes based on their ordinal number (podname-x)

- Deleting and/or scaling a StatefulSet down will not 
delete the volumes associated with the StatefulSet (preserving data)

- StatefulSet allow the stateful app to use DNS to find other peers (e.g. Cassandra clusters, 
ElasticSearch clusters, use DNS to find other member of the cluster), for example: 
cassandra-0.cassandra for all pods to reach the first node in the cassandra cluster

- Using StatefulSet you can run for instance 3 cassandra 
nodes on Kubernetes named cassandra-0 until cassandra-2

- StatefulSet allow stateful app to order the startup and teardown
When scaling up, it goes from 0 to n-1 (n = replication factor); when scaling down, it starts with the highest number.
This is useful if need to drain the data from a node before it can be shut down.
 

## Demo

In the demo, it install the cassandra with 3 nodes.
Basically they are same, but since we using the StatefulSet, it will raise 3 instance in order.
named cassandra-0, cassandra-1, cassandra-2.

In the template, we can see,
```
- name: CASSANDRA_SEEDS
  value: "cassandra-0.cassandra.default.svc.cluster.local"
```

