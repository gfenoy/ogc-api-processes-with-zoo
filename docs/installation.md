# Installing the ZOO-Project Helm chart on your platform

## Requirements

Before you begin, make sure you have the following tools installed and set up on your local environment:

### Skaffold

Skaffold is used to build, push, and deploy your application to Kubernetes. 

You can install it by following the instructions [here](https://skaffold.dev/docs/install/#standalone-binary).

### Helm

Helm is a package manager for Kubernetes, enabling you to manage Kubernetes applications easily. 

You can install it by following the steps [here](https://helm.sh/docs/intro/install/).

### Docker.Desktop (required for Apple silicon)

You can install it by following the steps [here](https://docs.docker.com/desktop/setup/install/mac-install/).

From Docker.Desktop, enable Kubernetes in the Kubernetes settings pannel. Click on "Apply & restart" button.

### Minikube (not required for Apple silicon)

Minikube runs a local Kubernetes cluster, ideal for development and testing. 

You can install it by following the guide [here](https://minikube.sigs.k8s.io/docs/start).

Start your minikube instance with:

```
minikube start
```

### Optional requirements

#### Kubectl

Kubectl is a command-line tool for interacting with Kubernetes clusters. It allows you to manage and inspect cluster resources. While not strictly required, it's highly recommended for debugging and interacting with your Kubernetes environment.

You can install it by following the instructions [here](https://kubernetes.io/docs/tasks/tools/#kubectl).

#### OpenLens

OpenLens is a graphical user interface for managing and monitoring Kubernetes clusters. It provides a visual way to interact with resources. 

While it's optional, it can significantly improve your workflow. You can download it [here](https://github.com/MuhammedKalkan/OpenLens?tab=readme-ov-file#installation).

### Add the helm repositories


```
helm repo add localstack https://helm.localstack.cloud
helm repo add zoo-project https://zoo-project.github.io/charts/
```

### Checking the requirements

After installing these tools, ensure they are available in your terminal by running the following commands:

```bash
skaffold version
helm version
# The following command is not required to work on Apple silicon
minikube version
```

If all commands return a version, you’re good to go!

## Deploying the workshop environment

For the purpose of this workshop, we will use the following GitHub repository: [dev-platform-eoap](https://github.com/eoap/dev-platform-eoap) from the EOAP organization.

Start the workshop environment.

````
git clone https://github.com/eoap/dev-platform-eoap.git
cd dev-platform-eoap/ogc-api-processes-with-zoo/
skaffold dev
# Apple user must use the additional options below
TAG=$(curl https://raw.githubusercontent.com/ZOO-Project/charts/refs/heads/main/zoo-project-dru/values.yaml | grep "zoofpm:" -A 4 | tail -n 1 | awk {'print $2'})
docker pull zooproject/zoo-project:$TAG --platform linux/amd64
docker pull zooproject/websocketd:67449315857b54bbc970f02c7aa4fd10a94721f0 --platform linux/amd64
skaffold dev -p macos --platform linux/amd64 --enable-platform-node-affinity=true
````


After some time you will see something like the following indicating that everything is in place.

````
No tags generated
Starting deploy...
Helm release zoo-project-dru not installed. Installing...
NAME: zoo-project-dru
LAST DEPLOYED: Thu Nov  6 16:28:56 2025
NAMESPACE: eoap-zoo-project
STATUS: deployed
REVISION: 1
NOTES:
🚀 ZOO-Project DRU deployed successfully!
=========================================

📍 Main Application Access:
  export POD_WS_NAME=$(kubectl get pods --namespace eoap-zoo-project -l "app.kubernetes.io/name=zoo-project-dru-websocketd,app.kubernetes.io/instance=zoo-project-dru-websocketd" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_WS_PORT=$(kubectl get pod --namespace eoap-zoo-project $POD_WS_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit ws://127.0.0.1:8888 to use your application"
  kubectl --namespace eoap-zoo-project port-forward $POD_WS_NAME 8888:$CONTAINER_WS_PORT &
  export POD_NAME=$(kubectl get pods --namespace eoap-zoo-project -l "app.kubernetes.io/name=zoo-project-dru,app.kubernetes.io/instance=zoo-project-dru" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace eoap-zoo-project $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace eoap-zoo-project port-forward $POD_NAME 8080:$CONTAINER_PORT
Helm release eoap-zoo-project-coder not installed. Installing...
NAME: eoap-zoo-project-coder
LAST DEPLOYED: Thu Nov  6 16:28:57 2025
NAMESPACE: eoap-zoo-project
STATUS: deployed
REVISION: 1
TEST SUITE: None
Helm release eoap-zoo-project-localstack not installed. Installing...
NAME: eoap-zoo-project-localstack
LAST DEPLOYED: Thu Nov  6 16:28:59 2025
NAMESPACE: eoap-zoo-project
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace "eoap-zoo-project" -l "app.kubernetes.io/name=localstack,app.kubernetes.io/instance=eoap-zoo-project-localstack" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace "eoap-zoo-project" $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace "eoap-zoo-project" port-forward $POD_NAME 8080:$CONTAINER_PORT
Waiting for deployments to stabilize...
 - eoap-zoo-project:deployment/zoo-project-dru-kubeproxy is ready. [8/9 deployment(s) still pending]
 - eoap-zoo-project:statefulset/zoo-project-dru-redis is ready. [7/9 deployment(s) still pending]
 - eoap-zoo-project:statefulset/zoo-project-dru-postgresql is ready. [6/9 deployment(s) still pending]
 - eoap-zoo-project:deployment/zoo-project-dru-websocketd is ready. [5/9 deployment(s) still pending]
 - eoap-zoo-project:deployment/code-server-deployment: FailedToRetrieveImagePullSecret: Unable to retrieve some image pull secrets (kaniko-secret); attempting to pull the image may not succeed.
    - eoap-zoo-project:pod/code-server-deployment-774cc68fc-8tvk7: FailedToRetrieveImagePullSecret: Unable to retrieve some image pull secrets (kaniko-secret); attempting to pull the image may not succeed.
      > [code-server-deployment-774cc68fc-8tvk7 init-file-on-volume] Cloning into 'ogc-api-processes-with-zoo'...
      > [code-server-deployment-774cc68fc-8tvk7 init-file-on-volume] [2025-11-06T15:29:02.515Z] info  Wrote default config file to /workspace/.config/code-server/config.yaml
      > [code-server-deployment-774cc68fc-8tvk7 init-file-on-volume] Installing extensions...
 - eoap-zoo-project:deployment/eoap-zoo-project-localstack: waiting for rollout to finish: 0 of 1 updated replicas are available...
 - eoap-zoo-project:deployment/zoo-project-dru-zoofpm: waiting for init container init-wait-for-dependencies-zoofpm to complete
    - eoap-zoo-project:pod/zoo-project-dru-zoofpm-5c579d7975-lgrxq: waiting for init container init-wait-for-dependencies-zoofpm to complete
      > [zoo-project-dru-zoofpm-5c579d7975-lgrxq init-wait-for-dependencies-zoofpm] Waiting for RabbitMQ to be ready with management API and definitions loaded...
      > [zoo-project-dru-zoofpm-5c579d7975-lgrxq init-wait-for-dependencies-zoofpm] Waiting for RabbitMQ management API...
 - eoap-zoo-project:deployment/zoo-project-dru-zookernel: waiting for init container init-wait-for-dependencies-zookernel to complete
    - eoap-zoo-project:pod/zoo-project-dru-zookernel-6958698bb8-8ccrd: waiting for init container init-wait-for-dependencies-zookernel to complete
      > [zoo-project-dru-zookernel-6958698bb8-8ccrd init-wait-for-dependencies-zookernel] Waiting for RabbitMQ to be ready with management API and definitions loaded...
      > [zoo-project-dru-zookernel-6958698bb8-8ccrd init-wait-for-dependencies-zookernel] Waiting for RabbitMQ management API...
 - eoap-zoo-project:statefulset/zoo-project-dru-rabbitmq: Waiting for 1 pods to be ready...
 - eoap-zoo-project:deployment/eoap-zoo-project-localstack is ready. [4/9 deployment(s) still pending]
 - eoap-zoo-project:statefulset/zoo-project-dru-rabbitmq is ready. [3/9 deployment(s) still pending]
 - eoap-zoo-project:deployment/code-server-deployment is ready. [2/9 deployment(s) still pending]
 - eoap-zoo-project:deployment/zoo-project-dru-zoofpm is ready. [1/9 deployment(s) still pending]
 - eoap-zoo-project:deployment/zoo-project-dru-zookernel is ready.
Deployments stabilized in 40.775 seconds
Port forwarding service/zoo-project-dru-websocketd in namespace eoap-zoo-project, remote port 8888 -> http://127.0.0.1:8888
Port forwarding service/code-server-service in namespace eoap-zoo-project, remote port 8080 -> http://localhost:8000
Port forwarding service/zoo-project-dru-service in namespace eoap-zoo-project, remote port 80 -> http://localhost:8080
No artifacts found to watch
Press Ctrl+C to exit
Watching for changes...
````

## Accessing the OGC API Processes Engine

From there, you can access the EOEPCA Processing - OGC API Processes Engine using the following URL: [http://localhost:8080](http://localhost:8080).

In addition, there is a Code Server available on [http://localhost:8000](http://localhost:8000) where you can find the notebooks.




