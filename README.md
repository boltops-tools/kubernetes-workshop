# Simple Kubernetes Workshop

In this workshop, we'll create these Kubernetes resources:

1. [deployment](deployment.yaml): Run nginx web server pods.
2. [service](service.yaml): Expose an endpoint to reach the nginx pods.

## Deploy

Commands:

    kubectl apply -f deployment.yaml
    kubectl apply -f service.yaml

Example with output:

    $ kubectl apply -f deployment.yaml
    deployment.apps/demo-web created
    $ kubectl apply -f service.yaml
    service/demo-web created
    $

## Explore

List all the Kubernetes resources created:

    $ kubectl get all
    NAME                            READY   STATUS    RESTARTS   AGE
    pod/demo-web-6454d6bc76-2kmvg   1/1     Running   0          56s
    pod/demo-web-6454d6bc76-ctplj   1/1     Running   0          56s

    NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
    service/demo-web     ClusterIP   192.168.0.89   <none>        80/TCP    52s
    service/kubernetes   ClusterIP   192.168.0.1   <none>        443/TCP   167m

    NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/demo-web   2/2     2            2           56s

    NAME                                  DESIRED   CURRENT   READY   AGE
    replicaset.apps/demo-web-6454d6bc76   2         2         2       56s
    $

Notice the replicaset is created by the deployment.  The replicaset in turn creates the pods. We only directly manage the deployment and service resources.

Hop into the pod container and `curl localhost`:

    $ kubectl exec -ti pod/demo-web-6454d6bc76-2kmvg bash
    # curl -s localhost | grep title
    <title>Welcome to nginx!</title>
    # curl -s 192.168.0.89 | grep title
    <title>Welcome to nginx!</title>
    # curl -s demo-web.default | grep title
    <title>Welcome to nginx!</title>
    # curl -s demo-web | grep title
    <title>Welcome to nginx!</title>
    # exit
    $

Notice we can curl with localhost, the service IP, or friendly DNS endpoints. The pretty endpoints are automatically created by Kubernetes.

## Clean Up

Let's clean up the resources:

    $ kubectl delete -f service.yaml
    service "demo-web" deleted
    $ kubectl delete -f deployment.yaml
    deployment.apps "demo-web" deleted
    $

Confirm all the resources have been removed:

    $ kubectl get all
    NAME                 TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   192.168.0.1   <none>        443/TCP   167m
    $
