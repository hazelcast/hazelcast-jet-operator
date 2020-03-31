# Hazelcast Jet Enterprise Opearator

Hazelcast Jet Enterprise is packaged with [Operator
Framework](https://github.com/operator-framework), which simplifies
deployment on Kubernetes. This is a step-by-step guide how
to deploy Hazelcast Jet Enterprise cluster together with Management
Center on your Kubernetes cluster.

## Prerequisites

- Kubernetes cluster (with admin rights) and the `kubectl` command
  configured (you may use
  [Minikube](https://kubernetes.io/docs/getting-started-guides/minikube/))

## Kubernetes Deployment steps

Below are the steps to start a Hazelcast Jet cluster using Operator
Framework. Note that the first 3 steps are usually performed only once
for the Kubernetes cluster (by the cluster admin). The step 4 is
performed each time you want to create a new Hazelcast Jet cluster.

Note: You need to clone this repository before following the next steps.

    git clone https://github.com/hazelcast/hazelcast-jet-operator.git
    cd hazelcast-jet-operator/hazelcast-jet-operator

### Step 1: Create RBAC

Run the following commands to configure the Operator permissions.

    kubectl apply -f operator-rbac.yaml

### Step 2: Create CRD (Custom Resource Definition)

To create the Hazelcast Jet resource definition, run the following command.

    kubectl apply -f hazelcastjetenterprisecluster.crd.yaml

### Step 3: Deploy Hazelcast Jet Enterprise Operator

Deploy Hazelcast Jet Operator with the following command.

    kubectl apply -f operator.yaml

### Step 4: Create Secret with Hazelcast License Key

Create a secret with your Hazelcast Jet Enterprise License Key. If you
don't have one, get a trial key from this [link](https://hazelcast.com/hazelcast-enterprise-download/trial/)
.

    $ kubectl create secret generic jet-license-key-secret --from-literal=key=your-license-key
    secret/jet-license-key-secret created

### Step 5: Start Hazelcast Jet Enterprise

Start Hazelcast Jet cluster with the following command.

    kubectl apply -f hazelcast-jet-enterprise.yaml

Your Hazelcast Jet Enterprise cluster (together with Hazelcast Jet
Management Center) should be created.

    $ kubectl get all
    NAME                                                                  READY   STATUS    RESTARTS   AGE
    pod/hazelcast-jet-enterprise-operator-df6bc9876-bmmfm                 1/1     Running   0          9m19s
    pod/jet-enterprise-cluster-hazelcast-jet-enterpri-management-cmq5fd   1/1     Running   0          8m56s
    pod/jet-enterprise-cluster-hazelcast-jet-enterprise-0                 1/1     Running   0          8m56s
    pod/jet-enterprise-cluster-hazelcast-jet-enterprise-1                 1/1     Running   0          8m11s

    NAME                                                                      TYPE           CLUSTER-IP      EXTERNAL-IP                                                              PORT(S)                        AGE
    service/hazelcast-jet-enterprise-operator-metrics                         ClusterIP      172.30.53.146   <none>                                                                   8686/TCP,8383/TCP              8m59s
    service/jet-enterprise-cluster-hazelcast-jet-enterpri-management-center   LoadBalancer   172.30.6.157    a219ca55647854404b7fb0e8b04d37c6-587626856.eu-west-3.elb.amazonaws.com   8081:31465/TCP,443:31788/TCP   8m56s
    service/jet-enterprise-cluster-hazelcast-jet-enterprise                   ClusterIP      None            <none>                                                                   5701/TCP                       8m56s

    NAME                                                                              READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/hazelcast-jet-enterprise-operator                                 1/1     1            1           9m19s
    deployment.apps/jet-enterprise-cluster-hazelcast-jet-enterpri-management-center   1/1     1            1           8m56s

    NAME                                                                                        DESIRED   CURRENT   READY   AGE
    replicaset.apps/hazelcast-jet-enterprise-operator-df6bc9876                                 1         1         1       9m19s
    replicaset.apps/jet-enterprise-cluster-hazelcast-jet-enterpri-management-center-b965b7c7c   1         1         1       8m56s

    NAME                                                               READY   AGE
    statefulset.apps/jet-enterprise-cluster-hazelcast-jet-enterprise   2/2     8m56s

**Note**: In `hazelcast-jet-enterprise.yaml` you can specify all parameters available
in the [Hazelcast Jet Enterprise Helm Chart](https://github.com/hazelcast/charts/tree/master/stable/hazelcast-jet-enterprise).

To connect to Hazelcast Jet Management Center, you can use `EXTERNAL-IP`
and open your browser at: `http://<EXTERNAL-IP>:8081` or `http://<EXTERNAL-IP>:443`
. If your Kubernetes environment does not have Load Balancer configured,
then please use `NodePort` or `Ingress`.

## Configuration

You may want to modify the behavior of the Hazelcast Jet Operator.

### Changing Hazelcast Jet and Hazelcast Jet Management Center version

If you want to modify the Hazelcast or Management Center version, update
`RELATED_IMAGE_HAZELCAST_JET` and `RELATED_IMAGE_MANAGEMENT_CENTER`
environment variables in `operator.yaml`.

### Configuring Hazelcast Cluster

You can check all configuration options in `hazelcast-jet-enterprise-full.yaml`.
Description of all parameters can be found
[here](https://github.com/hazelcast/charts/tree/master/stable/hazelcast-jet-enterprise#configuration).
