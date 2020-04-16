# PodPresets

https://www.udemy.com/course/learn-devops-the-complete-kubernetes-course/learn/lecture/11388080#overview

## Description
- Pod presets can inject information into pod at runtime
- It is used to inject kubernetes resources like secrets, configmaps, volumes and environment variables
- In case of having multiple applications which have a specific credentials, 
we can create 1 preset object which will inject an environment variable 
or config file to all matching pods, instead of edit all specifications and add the credentials.
- When injecting environment variables and volume mounts, 
the pod preset will apply the change to all containers within the pod
- If there is conflict, the podpreset will not be applied to the pod

## How to define and use ?
Define the preset, because it also have to mention selector (matchLabels in this case), 
it will only apply to the pod which match this criteria. 
```
apiVersion: settings.k8s.io/v1alpha1   # you might have to change this after PodPresets become stable 
kind: PodPreset
metadata:
  name: share-credential
spec:
  selector:
    matchLabels:
      app: myapp
  env:
    - name: MY_SECRET
      value: "123456"
  volumeMounts:
    - mountPath: /share
      name: share-volume
  volumes:
    - name: share-volume
      emptyDir: {}
```

# Alpha status
As long as the PodPresets is in alpha status, the following changes need to be made in kops:

```
spec:
  kubeAPIServer:
    enableAdmissionPlugins:
    - Initializers
    - NamespaceLifecycle
    - LimitRanger
    - ServiceAccount
    - PersistentVolumeLabel
    - DefaultStorageClass
    - DefaultTolerationSeconds
    - MutatingAdmissionWebhook
    - ValidatingAdmissionWebhook
    - NodeRestriction
    - ResourceQuota
    - PodPreset
    runtimeConfig:
      settings.k8s.io/v1alpha1: "true"

```

# running the demo
First apply the PodPresets:
```
kubectl create -f pod-presets.yaml
```

Then run the deployments
```
kubectl create -f deployments.yaml
```
