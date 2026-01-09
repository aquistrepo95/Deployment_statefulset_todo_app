# Kubernetes Deployment: sample to do application and Statefulset: MYSQL server.

## Functional Kubernetes infrastructure project running a single deployment replica for a to-do application and a single statefulset replica for a mysql:8.0 server for Database storage.

## This project showcases the following concepts in kubernetes:
* Deploying a stateless application using the Deployment controller.
* Deploying a stateful application using the Statefulset controller.
* Static storage creation using hostpath volumes NB: It is recommended to use Local volumes for more durability OR dynamic storage through storage classes for better provisioning.
* Persistent volumes and Persistent Volume claims for replicas (in this project for the mysql:8.0 replica)
* Config-maps to provide configuration information to both replicas i.e Deployment and Statefulset
* Secret to provide sensitive information to Statefulset replica.
* Headless service to route traffic to the Statefulset replica using the FQDN.
* Nodeport service to route traffic to the Deployment replica using a port on the Node, with clusterIp.

## Built with:
### Stateless Application:
 *  Javascript
### Stateless Application:
 * mysql:8.0
### Kubernetes
 * minikube
 * kubectl
### Docker

## This section will describe how to get the project running on your local machine.
* Download prebuilt Docker image from Docker Hub by running:
* Switch the Docker for terminal to use the minikube Docker daemon, pull the Docker Image, and switch back to your local Docker daemon 
  ```
   % eval $(minikube docker-env)
   % docker pull aquist9595/getstartedapp:0.0.1
   % eval $(minikube docker-env -u)
  ```
* Set up Volume on the Node:
  ```
   % minikube ssh
   % sudo cd ../../mnt/data
   % sudo mkdir -p sql-data
  ```
* Apply Persistent Volume - open another terminal, navigate to the directory you cloned this repo into and type the following commands:
  ```
   % kubectl apply -f persistent_vol.yaml
  ```
* Apply Config-maps and secrets:
  ```
   % kubectl apply -f gettingstarted_config_app.yaml
   % kubectl apply -f gettingstarted_config_sql.yaml
   % kubectl get cm
     NAME                            DATA   AGE
     gettingstarted-configvals-app   2      6d1h
     gettingstarted-configvals-sql   1      9d
   % kubectl apply -f gettingstarted_secret-app.yaml
   % kubectl apply -f gettingstarted_secret-sql.yaml
   % kubectl get secret
     NAME                         TYPE     DATA   AGE
     gettingstarted-secrets-app   Opaque   2      6d
     gettingstarted-secrets-sql   Opaque   1      9d
  ```
* Apply Deployment and Statefulset:
  ```
  % kubectl apply -f getting_started_app.yaml
  % kubectl apply -f getting_started_sql.yaml
  % kubectl get deploy,sts,po
    NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/gettingstarted-todo   1/1     1            1           5d3h

    NAME                                  READY   AGE
    statefulset.apps/gettingstarted-sql   1/1     8d

    NAME                                       READY   STATUS    RESTARTS       AGE
    pod/gettingstarted-sql-0                   1/1     Running   3 (6h9m ago)   8d
    pod/gettingstarted-todo-66fdd9fc88-jqw2n   1/1     Running   2 (6h8m ago)   5d3h
  ```
* Verify all components have been deployed successfully to the cluster:
  ```
  % kubectl get deploy,sts,pv,pvc,cm,secret,svc
    NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/gettingstarted-todo   1/1     1            1           5d3h

    NAME                                  READY   AGE
    statefulset.apps/gettingstarted-sql   1/1     8d

    NAME                                CAPACITY   ACCESS MODES   RECLAIM POLICY    STATUS   CLAIM                                STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
    persistentvolume/gettingstarted-pv   5Gi        RWO            Delete           Bound    default/data-gettingstarted-sql-0                           <unset>                   9d

    NAME                                                       STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
    persistentvolumeclaim/data-gettingstarted-sql-0            Bound    gettingstarted-pv   5Gi        RWO                           <unset>                 9d

    NAME                                      DATA   AGE
    configmap/gettingstarted-configvals-app   2      6d2h
    configmap/gettingstarted-configvals-sql   1      9d

    NAME                                TYPE     DATA   AGE
    secret/gettingstarted-secrets-app   Opaque   2      6d
    secret/gettingstarted-secrets-sql   Opaque   1      9d

    NAME                                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
    service/gettingstarted-app-svc        NodePort    10.100.118.81    <none>        90:32724/TCP     9d
    service/gettingstarted-sql-headless   ClusterIP   None             <none>        80/TCP           9d
  ```
  
