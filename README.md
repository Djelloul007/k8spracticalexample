# kubernetes practical example

# Project Description:

It is an online application that can calculate the Fibonacci sequence    <br/> 
https://github.com/Djelloul007/k8spracticalexample/blob/master/Fibonaccisequence.PNG
The application consists of 5 docker components: <br/>
1- Client: It is a Reactjs application for user interaction (see the code :Client/src) <br/>
2- Server: API server to read the data from the DB (Redis or Postgree) auszulesen (see the code Server/index.js) <br/>
3- Worker: Responsible for the Fibonacci sequence calculation (see the code: Worker/index.js) <br/>
4- Redis Docker container <br/>
5- Postgres Docker Container <br/>
As an alternative to client, server and worker you can use the 3 Docker images: <br/>
stephengrider/multi-client https://hub.docker.com/r/stephengrider/multi-client/ <br/>
stephengrider/multi-server https://hub.docker.com/r/stephengrider/multi-server/ <br/>
stephengrider/multi-worker https://hub.docker.com/r/stephengrider/multi-worker/ <br/>
To operate this microservice application, we are facing a Big challenge: <br/>

We must … <br/>

… Start container, stop container, monitoring container and update container <br/>
… Document the deployment steps <br/>
… manage the access data. <br/>
… manage the volume. <br/>
…expose the Applicatios. <br/>
… manage the network traffic. <br/>
… scale the applications up. <br/>
… monitor all containers and services. <br/>
… <br/>
And here comes kubernetes into play, all the challenges mentioned above can be orchestrated by Kubernetes. <br/>
Let's start with the basics: <br/>
In order to start play with Kubernetes, the Minikube tool from Google is suitable. <br/>
Minikube is a Kubernetescluster consisting of one node for the development. It is available on windows, mac and linux. https://kubernetes.io/docs/tasks/tools/install-minikube/ <br/>
  <br/>
The most important terms at kubernetes <br/>
1- Pods: A grouping of more than one container <br/>

Now let's create our first pods. To create a Pods we have 2 options. <br/>

1- Imperative mode „kubectl run multi-client --image=stephengrider/multi-client --port=3000“
To validate the Pods creation, enter the following command „kubectl get pods“ <br/>
2- Declarative mode using yaml file „infrastructure as code“ <br/>
apiVersion: apps/v1
kind: Deployment
metadata:
name: client-deployment
spec:
replicas: 1
selector:
matchLabels:
component: web
template:
metadata:
labels:
component: web
spec:
containers: - name: client
image: stephengrider/multi-client
ports: - containerPort: 3000
<br/>

to excute the desired pods on the yaml file we shoud run the folowing command
„kubectl apply -f .\client-deployment.yaml“ <br/>
2- Service: An abstract way to expose an application running on a set of Pods as a network service.
To acces to our the multi-client Container we muss expose the application using the service <br/>

apiVersion: v1
kind: Service
metadata:
name: client-cluster-ip-service
spec:
type: ClusterIP
selector:
component: web
ports: - port: 3000
targetPort: 3000
<br/>
to expose the application we shoud run the folowing command
„kubectl apply -f .\client-cluster-ip-service.yaml“ <br/>

3- Replicas: to define the number off the pods <br/>
4- ingress-service: exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource. <br/>

the yaml files for our Fibonacci sequence calculator project are in the k8s folder. <br/>
for every component we have 2 yaml file 1 one to deploy the container and the second one to expose the application. <br/>

- Client component: we have client-deployment.yaml and client-cluster-ip-service.yaml <br/>
- Server component. server-deployment.yaml & server-cluster-ip-service.yaml <br/>
- Redis Container: redis-deployment.yaml & server-cluster-ip-service.yaml <br/>
- Postgres Container: postgres-deployment.yaml & postgres-cluster-ip-service.yaml <br/>
- Worker component: worker-deployment.yaml file <br/>
- to manage the network trafic we need ingress-service.yaml file <br/>
- to manage the Volume we need database-persistent-volume-claim.yaml file <br/>
- to manage the Postgres database secret we need to run this command : „kubectl create secret generic pgpassword --from-literal PGPASSWORD=12345asdf“ <br/>

let now deploy our Fibonacci sequence calculator project. <br/>
We can do it in 2 steps <br/>

1- „kubectl create secret generic pgpassword --from-literal PGPASSWORD=12345asdf“ <br/>
2- kubectl apply -f .\ <br/>

to access the application just run „minikube ip“ to gegt the cluster ip address
& navigate to  this cluster IP adress with your browser   <br/>
you need to refresh the page after submit. <br/>

Thanks and have fun by trying
