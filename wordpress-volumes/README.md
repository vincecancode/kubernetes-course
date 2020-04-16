# Volumes autoprovisioning

- Allow Kubernetes to provision storage 
by create the volumes in cloud provider before attaching them to a node
- Done by using "StorageClass" object. For example, 
before would raise a general purpose storage in zone eu-west-1a  
```
   kind: StorageClass
   apiVersion: storage.k8s.io/v1beta1
   metadata:
     name: standard
   provisioner: kubernetes.io/aws-ebs
   parameters:
     type: gp2
     zone: eu-west-1a
```
- We have to create a claim for this volume. 
We specify which class to use ("inside annotation") and 
specify the access mode and volume size
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: db-storage
  annotations:
    volume.beta.kubernetes.io/storage-class: "standard"
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
```
- Finally, we create the pod to specify it. 
Specify the volumes refer to the claim "db-storage", and
mount the volume inside the container.

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: wordpress-db
spec:
  replicas: 1
  selector:
    app: wordpress-db
  template:
    metadata:
      name: wordpress-db
      labels:
        app: wordpress-db
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        args:
          - "--ignore-db-dir=lost+found"
        ports:
        - name: mysql-port
          containerPort: 3306
        env:
          - name: MYSQL_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: wordpress-secrets
                key: db-password
        volumeMounts:
        - mountPath: "/var/lib/mysql"
          name: mysql-storage
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: db-storage
```