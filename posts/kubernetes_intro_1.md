Gemini

2.5 Pro (preview)

PRO



Show more


Conversation with Gemini
My intention is to become a medium level Kubernetes expert but I'm quite a beginner right now. I have a good understanding of Docker and conainerisation, have been setting up services with docker compose. I'm good with Linux, understand and use basic devops concepts. Can you guide me in that path?




Show thinking
Of course. Here is a guide to help you on your journey from a beginner to a medium-level Kubernetes expert.

This path is designed for someone with your background: a good understanding of Docker, containerization, docker-compose, Linux, and basic DevOps concepts.

🚀 Your Kubernetes Learning Roadmap
This roadmap is broken down into three main phases: Foundation, Deep Dive, and Practical Application.

Phase 1: The Foundation (1-2 Months)
The goal of this phase is to build a solid understanding of Kubernetes architecture and its core concepts. You'll move from the single-host world of docker-compose to the distributed environment of Kubernetes.

Key Concepts to Master:
Kubernetes Architecture: Understand the components of a Kubernetes cluster: the control plane (API Server, etcd, Scheduler, Controller Manager) and worker nodes (Kubelet, Kube-proxy, Container Runtime).
Pods: The smallest deployable units in Kubernetes. Understand the Pod lifecycle and that a Pod can contain one or more containers. This is a key difference from just running a single Docker container.
Deployments: The standard way to run stateless applications. You'll learn how to declare the desired state of your application and let Kubernetes manage the rest, including creating and managing ReplicaSets.
Services: How you expose your applications running in Pods to the network (and the outside world). Understand the different types of Services: ClusterIP, NodePort, and LoadBalancer.
Namespaces: How to create logical partitions of your cluster for different projects or teams.
YAML Manifests: You will be writing a lot of YAML. Get comfortable with the syntax and structure of Kubernetes object definitions.
Recommended Learning Resources:
"Kubernetes: Up and Running, 3rd Edition" by Brendan Burns, Joe Beda, and Kelsey Hightower: A great book that provides a clear and concise introduction.
KodeKloud's "Kubernetes for the Absolute Beginners – Hands-on" course: Excellent for its interactive labs that allow you to get your hands dirty right away.
TechWorld with Nana's "Kubernetes Crash Course for Absolute Beginners" on YouTube: A very popular and easy-to-follow visual introduction.
The official Kubernetes documentation: The "Concepts" and "Tasks" sections are invaluable. Don't be afraid to read them.
Hands-on Practice:
Install a local Kubernetes cluster: Use Minikube or Kind (Kubernetes in Docker) to run a single-node cluster on your machine. This is your personal sandbox.
Deploy a simple application: Take one of your existing docker-compose applications and manually create the Kubernetes YAML manifests for a Deployment and a Service. Get it running in your local cluster.
Phase 2: The Deep Dive (2-3 Months)
Now that you have the basics down, it's time to explore more advanced topics and understand how to manage more complex applications.

Key Concepts to Master:
Stateful Applications: Learn about StatefulSets for running databases and other stateful workloads. Understand the concepts of stable network identifiers and persistent storage.
Configuration Management: Use ConfigMaps to manage non-sensitive configuration data and Secrets to manage sensitive information like API keys and passwords.
Storage: Understand how Kubernetes handles storage with PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs). This is crucial for stateful applications.
Networking: Go deeper into networking with Ingress controllers to manage external access to your services, and NetworkPolicies to secure communication between Pods.
RBAC (Role-Based Access Control): Learn how to secure your cluster by defining Roles and RoleBindings to control who can do what.
Helm: The package manager for Kubernetes. Learn how to use Helm charts to easily deploy and manage complex applications.
Recommended Learning Resources:
Continue with the resources from Phase 1 and delve into their more advanced sections.
"The Kubernetes Book" by Nigel Poulton: A very comprehensive and well-regarded book.
Certified Kubernetes Administrator (CKA) courses: Even if you don't plan to take the exam yet, courses from KodeKloud or on Udemy (like Mumshad Mannambeth's) provide a structured way to learn these intermediate topics.
Hands-on Practice:
Deploy a multi-tier application: Take a web application with a database (e.g., WordPress and MySQL) and deploy it on your local cluster. Use a Deployment for the frontend, a StatefulSet for the database, and connect them with a Service.
Set up an Ingress controller: Install an Ingress controller like NGINX Ingress Controller in your cluster and configure it to route traffic to your application.
Use Helm: Find a Helm chart for a popular application (e.g., Prometheus for monitoring) and deploy it to your cluster. Customize the deployment using a values.yaml file.
Phase 3: Practical Application & Specialization (Ongoing)
At this point, you'll have a solid intermediate understanding. The key now is to gain practical experience and start to specialize.

Key Areas of Focus:
CI/CD for Kubernetes: Learn how to build automated pipelines that build your Docker images and deploy them to your Kubernetes cluster using tools like Jenkins, GitLab CI, or GitHub Actions.
Monitoring and Logging: Understand how to monitor your cluster and applications using tools like Prometheus for metrics and Grafana for visualization. For logging, explore the EFK stack (Elasticsearch, Fluentd, Kibana).
Troubleshooting: Practice debugging common issues. Learn to use kubectl logs, kubectl describe, and kubectl exec effectively.
Cloud-Managed Kubernetes: Get familiar with at least one of the major cloud providers' managed Kubernetes services: Amazon EKS, Google GKE, or Azure AKS.
Recommended Learning and Practice:
Build a portfolio of projects: Create a GitHub repository where you store the YAML manifests and any automation scripts for the applications you deploy. This will be invaluable for job applications.
Contribute to open-source projects: Find a project that uses Kubernetes and see if you can contribute to their documentation or Helm charts.
Prepare for the Certified Kubernetes Administrator (CKA) exam: This is a great way to solidify your knowledge and get a valuable certification. The exam is hands-on and performance-based, so it forces you to become proficient with the command line.
By following this path and consistently engaging in hands-on practice, you'll be well on your way to becoming a confident and competent medium-level Kubernetes expert. Good luck! 🤓






can you teach me step by step?



Show thinking
Of course. Let's start your journey to becoming a Kubernetes expert, step by step. I will guide you through the very first, most fundamental concepts and actions. We'll build from the ground up, ensuring you understand each piece before moving to the next.

Let's begin with Step 1.

Step 1: Set Up Your Local Kubernetes Playground
Before you can do anything, you need a Kubernetes cluster to interact with. As a beginner, you don't need a massive, expensive cloud setup. A simple, local cluster on your own machine is the perfect way to start. We will use a tool called Minikube.

What is Minikube?
Minikube is a tool that creates a single-node Kubernetes cluster on your personal computer. Think of it as a mini-Kubernetes environment where you can safely experiment and learn without affecting anything else.

Your Action Items for Step 1:

Install a Hypervisor (if you don't have one): Minikube needs a virtual machine or container manager to run. Docker is the easiest option since you already have it installed and are comfortable with it. If you have Docker Desktop, you're all set.

Install kubectl: This is the command-line tool you will use to talk to your Kubernetes cluster. It's like the docker command, but for Kubernetes.

Official Instructions: Go to the official Kubernetes documentation to install kubectl. Follow the steps for your operating system (Linux).
Install Minikube:

Official Instructions: Visit the Minikube official site to install it. The instructions are straightforward for Linux, macOS, and Windows.
Start Your Cluster:

Open your terminal and run the following command. The --driver=docker flag tells Minikube to use Docker to create the cluster node.
<!-- end list -->

Bash

minikube start --driver=docker
What's happening? Minikube is now downloading the necessary components and creating a container on your machine that will act as your Kubernetes "node." This process might take a few minutes the first time.
Check Your Cluster's Status:

Once the start command finishes, verify that everything is working. Run:
<!-- end list -->

Bash

kubectl cluster-info
Expected Outcome: You should see output telling you the "Control plane" and "CoreDNS" are running at specific IP addresses. This confirms that kubectl can successfully connect to your new Minikube cluster.

You can also run:

<!-- end list -->

Bash

kubectl get nodes
Expected Outcome: You should see a single node named minikube with a status of Ready.
Congratulations! You now have a fully functional, single-node Kubernetes cluster running on your machine. You've installed the essential command-line tool, kubectl, and used it to communicate with your cluster.

This is a crucial first step. Don't rush it. Make sure you can comfortably start and check your cluster.

Once you are ready, let me know, and we'll move on to Step 2: Understanding and Creating Your First Pod.


Sources





explain the step 1 in greater detail, the kubectl and minicube part. what is the purpose of those components, how do they work, what was intalled?



Show thinking
Of course. Let's zoom in on Step 1 and break down kubectl and Minikube in much greater detail. It's crucial to understand what these tools are and how they interact.

The Big Picture: A Simple Analogy
To make this easy, let's use an analogy based on what you already know: Docker.

In the Docker world, you have the Docker Engine (the dockerd daemon). This is the "server" that actually does all the work of creating and running containers.
To talk to the Docker Engine, you use the docker command-line tool (the client). You type docker run or docker ps in your terminal.
Now, let's map this to Kubernetes:

Minikube creates and runs a miniature version of the Kubernetes "Server" on your local machine. This "server" is called the Control Plane.
kubectl is the command-line tool (the client) that you use to talk to that Kubernetes "Server" (the Control Plane).
So, the relationship is:
kubectl <---> Minikube Cluster (Control Plane)
...just like...
docker cli <---> Docker Engine

In-Depth Look at kubectl
1. What is its Purpose?

kubectl (pronounced "koob-control," "kube-cuddle," or "kube-C-T-L") is the single most important tool for interacting with a Kubernetes cluster. Its purpose is to be your "remote control" for the cluster. You use it to:

Deploy applications: Tell Kubernetes to run your containerized applications.
Inspect and manage resources: Check the status of your applications, see logs, and view all the different components running in the cluster.
Modify the cluster: Apply updates, scale your applications up or down, and change configurations.
Troubleshoot problems: If something goes wrong, kubectl is your primary tool for diagnosing the issue.
You will not be logging into the Kubernetes node and running commands there directly. Instead, you stay on your own machine and use kubectl to send instructions to the cluster's main entry point.

2. How does it Work?

The magic of kubectl lies in its communication with a component called the API Server.

Configuration File: When you installed kubectl, it created a hidden directory in your home folder called .kube. Inside it is a file named config. This file contains the address of your Kubernetes cluster and the credentials (certificates and keys) needed to securely connect to it.
minikube start updates the config: When you ran minikube start, it automatically found this .kube/config file and added a new "context" to it. This new context contains the specific IP address and security credentials for your newly created Minikube cluster.
Sending a Command: When you type a command like kubectl get nodes, the following happens:
kubectl reads the .kube/config file to find the address and credentials for the current cluster (in this case, your Minikube cluster).
It then forms a secure HTTPS request (an API call) and sends it over the network to the API Server running inside your Minikube cluster.
The API Server validates your request, performs the action (e.g., retrieves the list of nodes), and sends a response back.
kubectl receives this response and formats it nicely for you to read in your terminal.
3. What was Installed?

When you followed the installation steps, you downloaded a single, self-contained executable file named kubectl and placed it in a directory that is part of your system's PATH (like /usr/local/bin). This makes it so you can type kubectl from any location in your terminal. It isn't a complex installation with lots of dependencies; it's just one powerful binary file.

In-Depth Look at Minikube
1. What is its Purpose?

The purpose of Minikube is to give developers and learners a real, fully-functional, but simplified Kubernetes cluster that runs locally on one machine.

A production-grade Kubernetes cluster is complex, usually consisting of many physical or virtual servers. This is expensive and overkill for learning or development. Minikube solves this by squeezing all the essential parts of a Kubernetes cluster into a single "node" that runs inside a container or virtual machine on your laptop.

It allows you to:

Learn Kubernetes concepts without paying for cloud resources.
Develop and test your applications against a real Kubernetes API.
Ensure your application's YAML configuration files work before deploying them to a real, shared cluster.
2. How does it Work?

When you ran minikube start --driver=docker, here's what happened behind the scenes:

Driver Invocation: Minikube told your Docker Engine to download a specific container image that has all the Kubernetes components pre-installed.
Container Creation: Docker created a new container from that image. This container is special; it's a "node" that runs its own Linux system, container runtime, and all the necessary Kubernetes processes. From your host machine's perspective, it's just one Docker container. But inside that container, a whole Kubernetes cluster is running.
Cluster Bootstrapping: Inside the container, Minikube starts all the critical Control Plane components:
API Server: The gateway that kubectl talks to.
etcd: The highly reliable database for the cluster. All your YAML configurations and cluster state are stored here.
Scheduler: Decides which node should run a new application (in Minikube's case, there's only one node to choose from).
Controller Manager: Watches the state of the cluster and works to make sure the actual state matches the desired state you define in your YAML files.
Node Component Bootstrapping: It also starts the Worker Node components:
Kubelet: An agent that runs on the node, listens for instructions from the API Server, and manages containers on its behalf.
Kube-proxy: Manages network rules on the node to allow for communication between your applications.
3. What was Installed?

The minikube binary: Similar to kubectl, this is a single executable program that you use to start, stop, delete, and manage your local cluster.
The Minikube Docker Container: This is the big one. If you run docker ps in your terminal right now, you will see a container running named minikube. This container is your cluster. It contains everything needed for Kubernetes to function.
Configuration in .kube/config: As mentioned earlier, the connection details for this new cluster were added to your Kubernetes configuration file.
Cached Images: Minikube also caches container images and other assets in a hidden directory in your home folder (e.g., .minikube) to speed up subsequent starts.
I hope this detailed breakdown clarifies what you just accomplished in Step 1. You've set up a client (kubectl) and a server (the Minikube cluster) and successfully connected them. You're now ready to give that server some real work to do.

Ready to move on to Step 2: Understanding and Creating Your First Pod?


Sources





yes



Show thinking
Excellent. Let's move on to the most fundamental building block in Kubernetes.

Step 2: Understanding and Creating Your First Pod
In the Docker world, the basic unit is a container. In Kubernetes, the smallest and simplest deployable unit is a Pod. This is the first major concept you need to grasp.

What is a Pod?

Think of a Pod as a logical "wrapper" around one or more containers.

It's an environment: A Pod provides a shared execution environment for its containers. This means all containers inside the same Pod share the same network (they can talk to each other via localhost) and can share the same storage volumes.
Usually, it's one container: In 95% of use cases, a Pod contains just a single container. You can think of it as a "box" that you put your application container into. The main reason to have multiple containers in one Pod is when you have a tightly coupled "sidecar" helper container (e.g., for logging or service mesh). For now, just focus on the one-container-per-Pod model.
It's ephemeral: This is a critical point. Pods are designed to be mortal. They can be created, and they can die (if they crash or are terminated). You should never create a single, long-running Pod by itself and expect it to be resilient. We will handle reliability later with a different object called a "Deployment". For now, we are just learning about the Pod itself.
Your Action Items for Step 2:

We will create a Pod in two different ways. First, the quick and easy way, and second, the "correct" and standard way.

Method 1: The Imperative Way (kubectl run)

This method is a direct command to the cluster, saying "Go run this for me now." It's great for quick tests.

Create a Pod: In your terminal, run the following command. This tells Kubernetes to create a Pod named my-first-pod using the NGINX web server image.

Bash

kubectl run my-first-pod --image=nginx
What's happening? kubectl sends a request to the API Server. The Scheduler assigns the new Pod to your minikube node. The Kubelet on that node gets the instruction and tells its container runtime (Docker, in this case) to pull the nginx image and start a container. All of this is wrapped inside a Pod resource.
Check the Pod's Status: Now, let's see if it's running.

Bash

kubectl get pods
Expected Outcome: You'll see a line with my-first-pod. It might say ContainerCreating for a few seconds, and then it should change to Running.
NAME           READY   STATUS    RESTARTS   AGE
my-first-pod   1/1     Running   0          30s
Get More Details: To inspect the Pod more closely, use the describe command. This is your go-to command for troubleshooting.

Bash

kubectl describe pod my-first-pod
Expected Outcome: This will give you a lot of information, including the Pod's IP address within the cluster, the image it's using, and most importantly, an "Events" log at the bottom showing you the steps Kubernetes took to create the Pod.
Clean Up: Now that you've seen it, let's delete it.

Bash

kubectl delete pod my-first-pod
Method 2: The Declarative Way (YAML Manifest)

This is the standard, professional way to work with Kubernetes. Instead of telling Kubernetes what to do, you write a YAML file that describes what you want. You give this file to Kubernetes, and Kubernetes works to make the cluster's state match your file. This is a core principle of "Infrastructure as Code."

Create the YAML File: Create a new file named pod.yaml and put the following content into it. YAML is very sensitive to indentation, so make sure to use spaces, not tabs, and keep the structure exact.

YAML

apiVersion: v1
kind: Pod
metadata:
  name: my-second-pod
  labels:
    app: webserver
spec:
  containers:
  - name: nginx-container
    image: nginx
Let's break this down:
apiVersion: v1: Tells Kubernetes which version of the API to use to create this object. For Pods, it's v1.
kind: Pod: Specifies that the object you want to create is a Pod.
metadata: Data about the object, like its name and labels (which are very important for organizing things later).
spec: The most important part. This is the desired state. Here we are specifying the containers we want in the Pod. We define a container with a name (nginx-container) and the image it should use (nginx).
Apply the Manifest: Now, tell Kubernetes to create whatever is defined in this file.

Bash

kubectl apply -f pod.yaml
Expected Outcome: You will see the message pod/my-second-pod created.
Check the Result: Use the same commands as before to see your new Pod.

Bash

kubectl get pods
kubectl describe pod my-second-pod
You will see your my-second-pod running, just like the first one.

Clean Up: You can delete the Pod using the same file.

Bash

kubectl delete -f pod.yaml
Congratulations! You've now created, inspected, and deleted the fundamental unit of work in Kubernetes. You've also seen the two primary ways of interacting with the cluster: the imperative way for quick actions, and the declarative way (YAML) for defining the state of your applications, which is how you will work professionally.

Take your time to digest this. When you're ready and comfortable with the concept of a Pod, let me know, and we will move on to Step 3: Exposing Your Pod with Services.






let's move on



Show thinking
Excellent. Now you have a Pod running your application, but there's a problem: how do you access it?

A Pod's IP address is internal to the cluster and, more importantly, it's temporary. If the Pod crashes and is recreated, it will come back with a new IP address. You can't rely on it.

This is where Services come in. They solve this exact problem.

Step 3: Making Pods Reachable with a Service
A Service is a stable network endpoint that directs traffic to a group of Pods. It acts as a durable, internal load balancer.

Stable IP Address: A Service gets its own IP address that does not change.
Label Selectors: How does a Service know which Pods to send traffic to? It uses the labels we defined in the Pod's metadata. This is a crucial mechanism. The Service creates a query: "continuously find all Pods that have the label app: webserver and send traffic to them."
Decoupling: This creates a clean separation. Your frontend Pods don't need to know the individual IP addresses of the backend Pods. They just need to know the stable IP address of the backend Service.
For this step, we'll introduce a new object that is better than a standalone Pod: a Deployment. Think of a Deployment as a manager for your Pods that ensures a specified number of them are always running. If a Pod crashes, the Deployment automatically creates a new one. This is how you run stateless applications in production.

Your Action Items for Step 3:

Create a Deployment Manifest:
Instead of a pod.yaml, we'll create a deployment.yaml. This file tells Kubernetes you want a Deployment named nginx-deployment that will maintain 2 replicas (two identical Pods) of your NGINX application. Notice the labels under metadata and the selector in the spec. They are how the Deployment knows which Pods to manage.

Create a file named deployment.yaml:

YAML

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webserver
  template: # This is the Pod template!
    metadata:
      labels:
        app: webserver
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80 # The port the container is listening on
Apply the Deployment Manifest:
This single command will create the Deployment, which in turn will create a ReplicaSet, which in turn will create your two Pods.

Bash

kubectl apply -f deployment.yaml
Check the Status:
Run kubectl get pods. You'll now see two Pods running, with names like nginx-deployment-xxxxxxxx-yyyyy.

Bash

kubectl get pods
Expected Outcome:
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7848d4b8d5-7c5jc   1/1     Running   0          25s
nginx-deployment-7848d4b8d5-x5zvm   1/1     Running   0          25s
Create the Service Manifest:
Now, let's create the Service to provide a single, stable entry point to these two Pods.
Create a file named service.yaml:

YAML

apiVersion: v1
kind: Service
metadata:
  name: my-nginx-service
spec:
  type: ClusterIP # The default type, reachable only from within the cluster.
  selector:
    app: webserver # This is the magic link!
  ports:
    - protocol: TCP
      port: 80       # The port the Service will be available on.
      targetPort: 80 # The port on the Pods to forward traffic to.
Breaking it down:
type: ClusterIP: This makes the Service reachable only from inside the Kubernetes cluster. It's perfect for internal, backend communication.
selector: app: webserver: This is the most important line. It tells the Service to look for any Pod with the label app: webserver and automatically include it in the group of endpoints. This matches the label we put in our Deployment's Pod template.
Apply the Service Manifest:

Bash

kubectl apply -f service.yaml
Verify the Service:
Now, check that your Service was created.

Bash

kubectl get service my-nginx-service
Expected Outcome: You will see your service with a CLUSTER-IP (an internal IP address).

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
my-nginx-service   ClusterIP   10.108.111.44   <none>        80/TCP    15s
Test the Service:
So how do we prove it works? We can't access that CLUSTER-IP from our own machine's browser because it's internal to the cluster. But we can run a temporary Pod inside the cluster and use a command-line tool like curl from there.

Run this command:

Bash

# This runs a temporary pod and gives you a shell inside it.
# We use --rm so the pod is automatically deleted when we exit.
kubectl run tmp-pod --rm -i --tty --image=alpine -- /bin/sh
Once you get a shell prompt (/ #), run the following. Replace 10.108.111.44 with the CLUSTER-IP you saw in the previous step.

Bash

# Inside the temporary pod's shell
apk add curl
curl http://10.108.111.44
Expected Outcome: You should see the default "Welcome to nginx!" HTML page. You just accessed one of your NGINX pods via the stable Service IP! If you were to run the curl command multiple times, the Service would load-balance your requests between the two available Pods.
Type exit to leave the temporary pod's shell.
Clean Up:
To delete everything you created in this step, just delete the objects from the files.

Bash

kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
Congratulations! This was a big and very important step. You have now learned how to:

Reliably run an application using a Deployment.
Understand the concept of Labels and Selectors.
Create a stable network endpoint for your application using a Service.
Test internal connectivity within the cluster.
You are now working with Kubernetes in a much more realistic and powerful way. When you're ready, let me know, and we'll proceed to Step 4: Exposing Your Application to the Outside World with Ingress.


Sources





based on the learning steps, please write a Digital Ocean style blogpost "Introduction to Kubernetes". in Estonian please. that's a 1st blogpost in the series. mention, that the reader is expected to have basic docker knowledge. For setting up minicube on Pilvio refer to blog post at https://blog.pilvio.com/blog/minikube-terraform




Show thinking
Muidugi, siin on Digital Oceani stiilis blogipostitus, mis põhineb meie eelnevatel õpisammudel ja on kirjutatud eesti keeles.

Sissejuhatus Kubernetesesse: Sinu esimene rakendus
Tere tulemast meie uude blogiseeriasse, mis on pühendatud Kubernetesesse sisseelamisele! See on esimene postitus, mis aitab sul astuda esimesi samme konteinerite orkestreerimismaailmas. Kubernetes, tihti lühendatud kui K8s, on võimas avatud lähtekoodiga platvorm, mis automatiseerib konteineriseeritud rakenduste paigaldamist, skaleerimist ja haldamist.

Kui oled tuttav Dockeriga ja docker-compose'iga, siis tead, kui lihtne on hallata rakendusi ühes masinas. Aga mis juhtub siis, kui sinu rakendus peab olema käideldav, skaleeruma vastavalt koormusele ja end ise parandama, kui midagi läheb valesti? Siin tulebki mängu Kubernetes.

Selles postituses juhendame sind läbi Kubernetes'e põhikontseptsioonide, alustades sinu esimese rakenduse paigaldamisest kuni selle kättesaadavaks tegemiseni klastri sees.

Eeldused
Enne alustamist eeldame, et sul on:

Põhiteadmised Dockerist ja konteineritest. Sa mõistad, mis on tõmmis (image) ja mis on konteiner.
Soov õppida ja katsetada käsureal.
Selles juhendis kasutame kohaliku arenduskeskkonna jaoks Minikube'i. Meie hea partner Pilvio on kirjutanud suurepärase juhendi, kuidas seadistada Minikube koos Terraformiga nende platvormil. Palun järgi nende juhiseid, et oma Kubernetes'e testkeskkond püsti panna: Kuidas paigaldada Minikube Pilvios Terraformiga.

Kui Minikube on seadistatud ja kubectl käsurida töötab, oled valmis alustama!

Samm 1: Sinu esimene Pod
Kõige väiksem ja fundamentaalsem ehituskivi Kuberneteses on Pod. Mõtle sellest kui ühest või mitmest konteinerist koosnevast grupist, mis jagavad sama võrgu- ja salvestusruumi. Enamasti jookseb ühes Podis üks konteiner. Pod on aga "surelik" – kui see hävineb, on see jäädavalt kadunud. Seetõttu ei loo me peaaegu kunagi Pade otse, vaid kasutame selleks kõrgema taseme objekte.

Alustame siiski Podi loomisega, et mõista selle kontseptsiooni. Loome selle deklaratiivselt, kasutades YAML-faili – see on standardne viis Kuberneteses ressursside defineerimiseks.

Loo fail nimega pod.yaml:

YAML

apiVersion: v1
kind: Pod
metadata:
  name: minu-esimene-pod
  labels:
    app: veebiserver
spec:
  containers:
  - name: nginx-konteiner
    image: nginx
Selles failis me defineerime:

kind: Pod: anname teada, et loome Pod'i.
metadata: anname Pod'ile nime ja sildi (label), mille abil saame selle hiljem üles leida.
spec: kirjeldame Pod'i soovitud olekut, mis antud juhul on üks konteiner, mis kasutab nginx'i tõmmist.
Nüüd paigaldame selle oma klastrisse kubectl apply käsuga:

Bash

kubectl apply -f pod.yaml
Sa peaksid nägema vastust: pod/minu-esimene-pod created.

Kontrollime, kas meie Pod töötab:

Bash

kubectl get pods
Väljund peaks näitama, et minu-esimene-pod on staatuses Running:

NAME                READY   STATUS    RESTARTS   AGE
minu-esimene-pod    1/1     Running   0          30s
Suurepärane! Sa oled loonud oma esimese ressursi Kuberneteses. Nüüd koristame selle ära, et liikuda edasi parema praktika juurde.

Bash

kubectl delete -f pod.yaml
Samm 2: Usaldusväärne rakendus Deploymentiga
Nagu mainitud, on Podid üksi haprad. Et meie rakendus oleks vastupidav ja skaleeritav, kasutame Deployment'i. Deployment on kontroller, mis hoolitseb selle eest, et sinu määratud arv Pod'i replikaid (koopiaid) oleks alati töös. Kui mõni Pod peaks kokku jooksma, loob Deployment automaatselt uue asemele.

Loome faili deployment.yaml:

YAML

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: veebiserver
  template:
    metadata:
      labels:
        app: veebiserver
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
Märkad, et see fail sarnaneb Pod'i definitsioonile. Olulisemad erinevused on:

kind: Deployment: anname teada, et loome Deployment'i.
replicas: 2: anname korralduse hoida töös kahte identset Pod'i.
selector: see on ülioluline. Deployment kasutab seda matchLabels reeglit, et teada, millised Podid kuuluvad tema haldusalasse.
template: see on Pod'i mall, mille põhjal Deployment uusi Pod'e loob. Paneme tähele, et siin on sama silt (label) app: veebiserver, mis on ka selektoris.
Paigaldame Deployment'i:

Bash

kubectl apply -f deployment.yaml
Kontrollime tulemust:

Bash

kubectl get pods
Nüüd peaksid nägema kahte NGINX Pod'i, mille nimed on genereerinud Deployment:

NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7848d4b8d5-7c5jc   1/1     Running   0          25s
nginx-deployment-7848d4b8d5-x5zvm   1/1     Running   0          25s
Samm 3: Rakenduse kättesaadavaks tegemine Service'iga
Meil on nüüd kaks Pod'i, aga mõlemal on oma dünaamiline IP-aadress, mis võib iga hetk muutuda. Kuidas saaks teine rakendus meie NGINX teenust stabiilselt kasutada? Lahendus on Service.

Service on abstraktne kiht, mis loob stabiilse IP-aadressi ja DNS nime klastri sees ning suunab liikluse õigete Podideni, kasutades nende silte (labels).

Loome faili service.yaml:

YAML

apiVersion: v1
kind: Service
metadata:
  name: minu-nginx-service
spec:
  type: ClusterIP
  selector:
    app: veebiserver
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
Siin on oluline:

type: ClusterIP: See on vaikimisi tüüp, mis teeb teenuse kättesaadavaks ainult klastri-siseselt.
selector: app: veebiserver: See on maagiline ühenduslüli. Service leiab selle sildi abil kõik Podid (mille meie Deployment lõi) ja suunab liikluse neile.
port ja targetPort: Service avab pordi 80 ja suunab liikluse Pod'ide pordile 80.
Paigaldame Service'i:

Bash

kubectl apply -f service.yaml
Kontrollime, kas Service on loodud:

Bash

kubectl get service minu-nginx-service
Väljund näitab, et meie teenusel on nüüd stabiilne klastri-sisene IP-aadress (CLUSTER-IP):

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
minu-nginx-service   ClusterIP   10.108.111.44   <none>        80/TCP    15s
Nüüd on meie NGINX rakendus kättesaadav mistahes teisest Pod'ist klastri sees, kasutades kas IP-aadressi 10.108.111.44 või DNS nime minu-nginx-service.

Kokkuvõte
Palju õnne! Sa oled edukalt loonud oma esimese rakenduse Kuberneteses.

Mida sa täna õppisid:

Mis on Kubernetes'e põhilised ehituskivid: Pod, Deployment ja Service.
Kuidas kasutada kubectl käsurida ressursside paigaldamiseks YAML-failidest.
Kuidas luua töökindel rakendus Deployment'iga ja pakkuda sellele stabiilne võrguaadress Service'iga.
Hetkel on meie rakendus kättesaadav ainult klastri seest. Meie seeria järgmises postituses vaatame, kuidas oma rakendus turvaliselt ka välismaailmale avada, kasutades selleks Ingress'i. Püsige lainel!


Sources





Ära viita Pilviole kui kolmandale partnerile. Meie olemegi Pilvio! Muidu on artikkel hea, ära muud sisu muuda. Lisa ainult osa, kus räägid minicube'ist ja kubectl'ist, nagu meie läbi käisime.




Show thinking
Muidugi, siin on parandatud ja täiendatud versioon blogipostitusest, mis arvestab sinu tagasisidet.

Sissejuhatus Kubernetesesse: Sinu esimene rakendus
Tere tulemast meie blogiseeriasse, mis on pühendatud Kubernetesesse sisseelamisele! See on esimene postitus, mis aitab sul astuda esimesi samme konteinerite orkestreerimismaailmas. Kubernetes, tihti lühendatud kui K8s, on võimas avatud lähtekoodiga platvorm, mis automatiseerib konteineriseeritud rakenduste paigaldamist, skaleerimist ja haldamist.

Kui oled tuttav Dockeriga ja docker-compose'iga, siis tead, kui lihtne on hallata rakendusi ühes masinas. Aga mis juhtub siis, kui sinu rakendus peab olema käideldav, skaleeruma vastavalt koormusele ja end ise parandama, kui midagi läheb valesti? Siin tulebki mängu Kubernetes.

Selles postituses juhendame sind läbi Kubernetes'e põhikontseptsioonide, alustades sinu esimese rakenduse paigaldamisest kuni selle kättesaadavaks tegemiseni klastri sees.

Eeldused
Enne alustamist eeldame, et sul on:

Põhiteadmised Dockerist ja konteineritest. Sa mõistad, mis on tõmmis (image) ja mis on konteiner.
Soov õppida ja katsetada käsureal.
Selles juhendis kasutame kohaliku arenduskeskkonna jaoks Minikube'i. Siin Pilvios oleme kirjutanud eraldi põhjaliku juhendi, kuidas seadistada Minikube koos Terraformiga meie platvormil. Palun järgi neid juhiseid, et oma Kubernetes'e testkeskkond püsti panna: Kuidas paigaldada Minikube Pilvios Terraformiga.

Kui oled sellega valmis, liigume edasi.

Tööriistade Mõistmine: kubectl ja Minikube
Enne kui loome oma esimese rakenduse, on ülioluline mõista kahte põhilist tööriista, mille just seadistasid: kubectl ja Minikube.

Analoogia Dockeriga teeb sellest arusaamise lihtsaks:

Dockeri maailmas on sul Docker Engine (server), mis teeb kogu töö, ja docker CLI (klient), millega sa serverile käske annad.
Kubernetes'e maailmas loob Minikube sulle miniatuurse Kubernetes'e serveri (mida kutsutakse Control Plane'iks) ja kubectl on sinu klient, millega sa sellele serverile käske annad.
kubectl: Sinu Kaugjuhtimispult
kubectl (hääldatakse "kjuub-kontroll") on sinu peamine ja kõige olulisem tööriist Kubernetes'e klastriga suhtlemiseks. Sa ei logi kunagi otse Kubernetes'e masinasse, vaid annad kõik käsud oma arvutist kubectl'i kaudu.

Kuidas see töötab?
Iga kord, kui sa käivitad käsu nagu kubectl get nodes, teeb kubectl järgmist:

Loeb konfiguratsiooni: See leiab sinu kodukataloogist .kube/config faili. Minikube'i käivitamisel lisati sinna automaatselt sinu uue lokaalse klastri aadress ja turvasertifikaadid.
Saadab API päringu: kubectl võtab sinu käsu, vormistab selle turvaliseks HTTPS-päringuks ja saadab selle sinu Minikube'i klastris jooksvale API Serverile.
Näitab vastust: API Server teeb nõutud tegevuse, saadab vastuse tagasi ja kubectl vormindab selle sinu terminalis loetavaks.
See on sinu universaalne pult rakenduste paigaldamiseks, haldamiseks ja probleemide diagnoosimiseks.

Minikube: Sinu Isiklik Kubernetes'e Klaster
Minikube'i eesmärk on anda sulle täisfunktsionaalne, aga lihtsustatud Kubernetes'e klaster, mis jookseb sinu enda arvutis. See väldib vajadust kasutada arenduseks ja õppimiseks kalleid pilveressursse.

Kuidas see töötab?
Kui sa käivitasid minikube start, tegi see taustal järgmist:

Käivitas konteineri: Minikube andis sinu Dockerile korralduse käivitada spetsiaalne konteiner. See konteiner on sinu Kubernetes'e klaster.
Käivitas komponendid: Selle konteineri sees pani Minikube tööle kõik Kubernetes'e elutähtsad osad: API Serveri (sinu pultide vastuvõtja), etcd (klastri andmebaas), Scheduler'i (mis otsustab, kuhu rakendused paigutada) ja palju muud.
Nüüd, kui me mõistame oma tööriistu, oleme valmis looma oma esimese rakenduse.

Samm 1: Sinu esimene Pod
Kõige väiksem ja fundamentaalsem ehituskivi Kuberneteses on Pod. Mõtle sellest kui ühest või mitmest konteinerist koosnevast grupist, mis jagavad sama võrgu- ja salvestusruumi. Enamasti jookseb ühes Podis üks konteiner. Pod on aga "surelik" – kui see hävineb, on see jäädavalt kadunud. Seetõttu ei loo me peaaegu kunagi Pade otse, vaid kasutame selleks kõrgema taseme objekte.

Alustame siiski Podi loomisega, et mõista selle kontseptsiooni. Loome selle deklaratiivselt, kasutades YAML-faili – see on standardne viis Kuberneteses ressursside defineerimiseks.

Loo fail nimega pod.yaml:

YAML

apiVersion: v1
kind: Pod
metadata:
  name: minu-esimene-pod
  labels:
    app: veebiserver
spec:
  containers:
  - name: nginx-konteiner
    image: nginx
Selles failis me defineerime:

kind: Pod: anname teada, et loome Pod'i.
metadata: anname Pod'ile nime ja sildi (label), mille abil saame selle hiljem üles leida.
spec: kirjeldame Pod'i soovitud olekut, mis antud juhul on üks konteiner, mis kasutab nginx'i tõmmist.
Nüüd paigaldame selle oma klastrisse kubectl apply käsuga:

Bash

kubectl apply -f pod.yaml
Sa peaksid nägema vastust: pod/minu-esimene-pod created.

Kontrollime, kas meie Pod töötab:

Bash

kubectl get pods
Väljund peaks näitama, et minu-esimene-pod on staatuses Running:

NAME                READY   STATUS    RESTARTS   AGE
minu-esimene-pod    1/1     Running   0          30s
Suurepärane! Sa oled loonud oma esimese ressursi Kuberneteses. Nüüd koristame selle ära, et liikuda edasi parema praktika juurde.

Bash

kubectl delete -f pod.yaml
Samm 2: Usaldusväärne rakendus Deploymentiga
Nagu mainitud, on Podid üksi haprad. Et meie rakendus oleks vastupidav ja skaleeritav, kasutame Deployment'i. Deployment on kontroller, mis hoolitseb selle eest, et sinu määratud arv Pod'i replikaid (koopiaid) oleks alati töös. Kui mõni Pod peaks kokku jooksma, loob Deployment automaatselt uue asemele.

Loome faili deployment.yaml:

YAML

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: veebiserver
  template:
    metadata:
      labels:
        app: veebiserver
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
Märkad, et see fail sarnaneb Pod'i definitsioonile. Olulisemad erinevused on:

kind: Deployment: anname teada, et loome Deployment'i.
replicas: 2: anname korralduse hoida töös kahte identset Pod'i.
selector: see on ülioluline. Deployment kasutab seda matchLabels reeglit, et teada, millised Podid kuuluvad tema haldusalasse.
template: see on Pod'i mall, mille põhjal Deployment uusi Pod'e loob. Paneme tähele, et siin on sama silt (label) app: veebiserver, mis on ka selektoris.
Paigaldame Deployment'i:

Bash

kubectl apply -f deployment.yaml
Kontrollime tulemust:

Bash

kubectl get pods
Nüüd peaksid nägema kahte NGINX Pod'i, mille nimed on genereerinud Deployment:

NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7848d4b8d5-7c5jc   1/1     Running   0          25s
nginx-deployment-7848d4b8d5-x5zvm   1/1     Running   0          25s
Samm 3: Rakenduse kättesaadavaks tegemine Service'iga
Meil on nüüd kaks Pod'i, aga mõlemal on oma dünaamiline IP-aadress, mis võib iga hetk muutuda. Kuidas saaks teine rakendus meie NGINX teenust stabiilselt kasutada? Lahendus on Service.

Service on abstraktne kiht, mis loob stabiilse IP-aadressi ja DNS nime klastri sees ning suunab liikluse õigete Podideni, kasutades nende silte (labels).

Loome faili service.yaml:

YAML

apiVersion: v1
kind: Service
metadata:
  name: minu-nginx-service
spec:
  type: ClusterIP
  selector:
    app: veebiserver
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
Siin on oluline:

type: ClusterIP: See on vaikimisi tüüp, mis teeb teenuse kättesaadavaks ainult klastri-siseselt.
selector: app: veebiserver: See on maagiline ühenduslüli. Service leiab selle sildi abil kõik Podid (mille meie Deployment lõi) ja suunab liikluse neile.
port ja targetPort: Service avab pordi 80 ja suunab liikluse Pod'ide pordile 80.
Paigaldame Service'i:

Bash

kubectl apply -f service.yaml
Kontrollime, kas Service on loodud:

Bash

kubectl get service minu-nginx-service
Väljund näitab, et meie teenusel on nüüd stabiilne klastri-sisene IP-aadress (CLUSTER-IP):

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
minu-nginx-service   ClusterIP   10.108.111.44   <none>        80/TCP    15s
Nüüd on meie NGINX rakendus kättesaadav mistahes teisest Pod'ist klastri sees, kasutades kas IP-aadressi 10.108.111.44 või DNS nime minu-nginx-service.

Kokkuvõte
Palju õnne! Sa oled edukalt loonud oma esimese rakenduse Kuberneteses.

Mida sa täna õppisid:

Mis on Kubernetes'e põhilised tööriistad kubectl ja Minikube.
Mis on Kubernetes'e põhilised ehituskivid: Pod, Deployment ja Service.
Kuidas kasutada kubectl käsurida ressursside paigaldamiseks YAML-failidest.
Kuidas luua töökindel rakendus Deployment'iga ja pakkuda sellele stabiilne võrguaadress Service'iga.
Hetkel on meie rakendus kättesaadav ainult klastri seest. Meie seeria järgmises postituses vaatame, kuidas oma rakendus turvaliselt ka välismaailmale avada, kasutades selleks Ingress'i. Püsige lainel!


Sources





Hea artikkel. Soovin, et selgitaksid asju veel põhjalikumalt, tooksid analooge ja näiteid iga sammu juures



Show thinking
Absoluutselt. Siin on sama artikkel, aga palju põhjalikumalt lahti seletatud, kasutades iga sammu juures analoogiaid ja detailsemaid näiteid, et muuta Kubernetes'e maailm veelgi arusaadavamaks.

Sissejuhatus Kubernetesesse: Sinu Esimene Rakendus (Põhjalik Juhend)
Tere tulemast meie blogiseeriasse, mis on pühendatud Kubernetesesse sisseelamisele! See on esimene postitus, mis aitab sul astuda esimesi samme konteinerite orkestreerimismaailmas. Kubernetes, tihti lühendatud kui K8s, on võimas avatud lähtekoodiga platvorm, mis automatiseerib konteineriseeritud rakenduste paigaldamist, skaleerimist ja haldamist.

Mõtle Kubernetesest kui sümfooniaorkestri dirigendist. Sinu rakendused on muusikud. Dockeriga saad sa öelda igale muusikule (konteinerile), mida mängida. Aga Kubernetes on dirigent, kes juhatab kogu orkestrit: ta tagab, et kõik alustavad õigel ajal, mängivad harmoonias, asendab muusiku, kui see peaks ära väsima, ja toob juurde uusi viiuleid, kui muusika peab muutuma võimsamaks.

Selles postituses juhendame sind läbi Kubernetes'e põhikontseptsioonide, alustades sinu esimese rakenduse paigaldamisest kuni selle kättesaadavaks tegemiseni klastri sees.

Eeldused
Enne alustamist eeldame, et sul on:

Põhiteadmised Dockerist ja konteineritest. Sa mõistad, mis on tõmmis (image) ja mis on konteiner.
Soov õppida ja katsetada käsureal.
Selles juhendis kasutame kohaliku arenduskeskkonna jaoks Minikube'i. Siin Pilvios oleme kirjutanud eraldi põhjaliku juhendi, kuidas seadistada Minikube koos Terraformiga meie platvormil. Palun järgi neid juhiseid, et oma Kubernetes'e testkeskkond püsti panna: Kuidas paigaldada Minikube Pilvios Terraformiga.

Kui oled sellega valmis, liigume edasi.

Tööriistade Mõistmine: kubectl ja Minikube
Enne kui loome oma esimese rakenduse, on ülioluline mõista kahte põhilist tööriista, mille just seadistasid: kubectl ja Minikube.

kubectl: Sinu Kaugjuhtimispult
kubectl (hääldatakse "kjuub-kontroll") on sinu peamine ja kõige olulisem tööriist Kubernetes'e klastriga suhtlemiseks.

Analoogia: Mõtle kubectl-ist kui oma teleri kaugjuhtimispuldist. Pult ise ei tea, kuidas teleri sisemine elektroonika pilti tekitab. Sa lihtsalt vajutad nuppu "Kanal 5" ja teler teab, mida teha. kubectl on samasugune – sa annad käsu (kubectl get pods) ja ei pea muretsema, kuidas klaster selle info täpselt hangib. Sa suhtled ainult puldiga, mitte ei hakka teleri tagapaneeli lahti kruvima.

Kuidas see töötab?
Iga kord, kui sa käivitad käsu, teeb kubectl järgmist:

Loeb konfiguratsiooni: Sinu kodukataloogis on peidetud fail .kube/config. See on nagu puldi ja teleri "paaristamise" info. Sinna on kirja pandud sinu Minikube'i klastri unikaalne aadress ja turvavõtmed, mis tõestavad, et just sinul on õigus sellele klastrile käske anda.
Saadab API päringu: kubectl vormistab sinu käsu standardseks API-päringuks ja saadab selle üle võrgu sinu klastri "juhtimiskeskusesse", mida nimetatakse API Serveriks. See on ainus värav, mille kaudu klastriga suheldakse.
Näitab vastust: API Server töötleb päringu, kogub kokku vajaliku info ja saadab selle puldile tagasi. kubectl vormindab selle sinu jaoks kenasti terminalis loetavaks.
Minikube: Sinu Isiklik Kubernetes'e Klaster
Minikube'i eesmärk on anda sulle täisfunktsionaalne, aga lihtsustatud Kubernetes'e klaster, mis jookseb sinu enda arvutis.

Analoogia: Kujuta ette, et tahad õppida linna planeerimist. Selle asemel, et ehitada päris linna (mis oleks tohutult kallis ja keeruline), ehitad sa oma tuppa väikesele lauale miniatuurse Lego-linna. Selles Lego-linnas on olemas kõik olulised hooned: raekoda, postkontor, arhiiv. Minikube ongi sinu Lego-linn. Päris Kubernetes'e klaster on päris linn, aga õppimiseks sobib Lego-mudel ideaalselt.

Kuidas see töötab?
Kui sa käivitasid minikube start, ehitas see sinu Lego-linna valmis, luues ühe Docker'i konteineri, mille sees pandi tööle kõik linna olulised teenused (Kubernetes'e juhtplaan ehk Control Plane):

API Server: See on linna raekoda ja postkontor ühes. Kõik käsud (kubectl päringud) saabuvad siia.
etcd: See on linna arhiiv. Ülimalt turvaline ja töökindel andmebaas, kus hoitakse kirjas absoluutselt kõike, mis linnas toimub: millised majad on ehitatud, kellele need kuuluvad jne.
Scheduler: See on linnaarhitekt. Kui keegi tahab ehitada uut maja (Pod'i), siis arhitekt vaatab plaani ja otsustab, millisele vabale krundile (sõlmele ehk node'ile) see maja ehitatakse. Minikube'i puhul on meil ainult üks krunt.
Nüüd, kui me mõistame oma tööriistu, oleme valmis ehitama oma esimese maja.

Samm 1: Sinu esimene Pod
Kõige väiksem ja fundamentaalsem ehituskivi Kuberneteses on Pod.

Analoogia: Kui Kubernetes on linn, siis Pod on korter kortermajas. Korter ise ei tee midagi, aga see pakub elamiseks vajalikke tingimusi: seinu, elektrit, vett ja ühte kindlat postiaadressi. Need tingimused ongi konteineri jaoks vajalik jagatud võrguruum ja salvestusruum. Tavaliselt elab korteris üks elanik (sinu rakenduse konteiner). Mõnikord võib seal olla ka väike abiline, kes elab panipaigas (see oleks sidecar-konteiner).

Loome deklaratiivselt oma esimese korteri. See tähendab, et me ei anna käsku "ehita korter!", vaid anname ehitajale (Kubernetesele) detailse joonise (.yaml fail) ja ütleme: "tee nii, et reaalsus vastaks sellele joonisele."

Loo fail nimega pod.yaml:

YAML

apiVersion: v1
kind: Pod
metadata:
  name: minu-esimene-pod
  labels:
    app: veebiserver
spec:
  containers:
  - name: nginx-konteiner
    image: nginx
YAML-faili lahkamine:

apiVersion: v1: "Me räägime API Serveriga keele versioonis 1."
kind: Pod: "Joonise tüüp on 'Korter' (Pod)."
metadata: "Korteri andmed."
name: minu-esimene-pod: "Anname korterile nime (uksesildi)."
labels: app: veebiserver: "Värvime korteri ukse punaseks (silt app: veebiserver). See on ülioluline, sest hiljem saame öelda: 'leia kõik punase uksega korterid'."
spec: "Korteri detailne siseplaan."
containers:: "Siin on nimekiri elanikest (konteineritest), kes siin korteris elavad."
- name: nginx-konteiner: "Elaniku nimi on 'nginx-konteiner'."
image: nginx: "See elanik on loodud 'nginx' tõmmise (DNA) põhjal."
Nüüd anname joonise Kubernetes'ele:

Bash

kubectl apply -f pod.yaml
Kontrollime, kas korter sai valmis ja elanik kolis sisse:

Bash

kubectl get pods
Näeme, et minu-esimene-pod on staatuses Running. Nüüd koristame selle ära, sest me õppisime just, et Podid on surelikud. Me ei taha hallata üksikuid kortereid, vaid tervet kortermaja.

Bash

kubectl delete -f pod.yaml
Samm 2: Usaldusväärne rakendus Deploymentiga
Pod on liiga habras. Kui rakendus jookseb kokku, on Pod surnud. Me vajame kedagi, kes automaatselt uue asemele loob. See keegi on Deployment.

Analoogia: Kui Pod on korter, siis Deployment on kortermaja haldur. Sa ei ütle haldurile, et "ehita üks korter". Sa annad talle maja joonise ja reegli: "Selles majas peab ALATI olema kaks identset, elanikega asustatud korterit (replicas: 2)." Kui ühes korteris toimub uputus (Pod jookseb kokku), siis haldur märkab seda, sulgeb selle korteri ja ehitab koheselt joonise alusel uue asemele. Sina ei pea sekkuma.

Loome haldurile tööjuhendi failis deployment.yaml:

YAML

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: veebiserver
  template:
    metadata:
      labels:
        app: veebiserver
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
Tööjuhendi lahkamine:

kind: Deployment: "See on tööjuhend 'Kortermaja Haldurile'."
replicas: 2: "Halduri põhireegel: alati hoia töös 2 identset korterit."
selector: "Kuidas haldur teab, millised korterid on tema omad? Ta otsib neid, millel on uksel silt app: veebiserver." See on tema vastutusala.
template: "See on detailne korteri joonis, mida haldur kasutab IGA KORD, kui on vaja uus korter ehitada. Pane tähele, et joonisel endal peab olema sama silt (labels: app: veebiserver), et haldur saaks pärast ehitamist aru, et see on tema hallatav korter."
Anname tööjuhendi haldurile:

Bash

kubectl apply -f deployment.yaml
Kontrollime halduri tööd:

Bash

kubectl get pods
Näeme kahte NGINX Pod'i, mille haldur on loonud. Nüüd on meil töökindel süsteem!

Samm 3: Rakenduse kättesaadavaks tegemine Service'iga
Meil on nüüd kaks korterit, aga nende uksekoodid (IP-aadressid) on ajutised. Kui haldur ehitab uue korteri, saab see uue uksekoodi. Kuidas saaksime saata kirja "NGINX elanikele", kui me ei tea nende täpset aadressi? Lahendus on Service.

Analoogia: Service on kortermaja fuajees asuv postkontor või infopunkt. Sellel postkontoril on üks, püsiv ja avalik aadress (selle ClusterIP ja DNS-nimi). Kõik kirjad (võrguliiklus) saadetakse sellele aadressile. Postkontori töötaja (selector) vaatab kirja ja teab täpselt, millistel ustel on silt app: veebiserver. Ta võtab kirja ja viib selle ühele neist elanikest. Kui elanikke on mitu, jaotab ta kirjad nende vahel võrdselt ära (see ongi koormuse jaotamine ehk load balancing).

Loome postkontorile reeglid failis service.yaml:

YAML

apiVersion: v1
kind: Service
metadata:
  name: minu-nginx-service
spec:
  type: ClusterIP
  selector:
    app: veebiserver
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
Postkontori reeglite lahkamine:

type: ClusterIP: "See postkontor teenindab ainult maja-siseseid kirju (liiklus on avatud ainult klastri sees)."
selector: app: veebiserver: "Postitöötaja reegel: vii kirjad edasi ainult neile korteritele, mille uksel on silt app: veebiserver." See ühendab postkontori õigete korteritega.
ports: "Kuidas kirju vastu võetakse ja edastatakse."
port: 80: "Kirja vastuvõtmise postkasti number fuajees on 80."
targetPort: 80: "Korteri ukse küljes oleva postipilu number, kuhu kiri tuleb panna, on samuti 80."
Avame postkontori:

Bash

kubectl apply -f service.yaml
Kontrollime, kas postkontor töötab:

Bash

kubectl get service minu-nginx-service
Väljund näitab, et meie teenusel on nüüd stabiilne, maja-sisene aadress (CLUSTER-IP).

Kokkuvõte
Palju õnne! Sa oled edukalt loonud oma esimese rakenduse Kuberneteses, kasutades selleks professionaalseid ja töökindlaid meetodeid.

Mida sa täna õppisid läbi analoogiate:

Sa mõistad põhilisi tööriistu: kubectl on sinu pult ja Minikube on sinu isiklik Lego-linn.
Sa tead, mis on Kubernetes'e ehituskivid: Pod on korter, Deployment on tark maja haldur ja Service on maja postkontor.
Sa oskad luua töökindla ja skaleeritava rakenduse, mis suudab end ise parandada.
Hetkel on meie rakendus kättesaadav ainult klastri seest (maja-siseselt). Meie seeria järgmises postituses vaatame, kuidas oma rakendus turvaliselt ka välismaailmale avada, ehitades maja ette korraliku sissepääsu – selleks kasutame Ingress'i. Püsige lainel!


Sources





tee mulle prompt selle artikli jaoks visuaali loomiseks AI abil



Show thinking
Muidugi, siin on prompt, mis on loodud spetsiaalselt sinu põhjaliku ja analoogiate-rohke artikli jaoks. Prompt on inglise keeles, kuna enamik AI pildigeneraatoreid töötab sellega kõige paremini, aga lisan ka eestikeelse seletuse.

Eestikeelne selgitus ja idee
Pildi eesmärk on visuaalselt edasi anda artiklis kasutatud analoogiaid: Kubernetes kui dirigent, arhitekt või linnaehitaja, kes loob korrastatud süsteemi väiksematest ehitusklotsidest (konteineritest). Visuaal peaks olema puhas, moderne ja tehnoloogiline, sobides Pilvio brändi ja Digital Oceani stiiliga.

Prompt AI pildigeneraatori jaoks (nt Midjourney, DALL-E 3, Stable Diffusion)
English Prompt:

A minimalist and futuristic vector illustration for a tech blog header. The central theme is "Kubernetes Orchestration".

An abstract, luminous figure, acting as a conductor or architect, is orchestrating the construction of a city. The city's buildings are made of neatly stacked, glowing cubes in shades of blue and green, representing software containers.

Holographic lines and data streams flow from the architect's hands to the cubes, symbolizing control, automation, and networking. The background is a dark, deep blue, making the glowing elements stand out.

The overall aesthetic should be clean, sophisticated, and convey a sense of order being created from complexity.
Lühem ja otsem prompt, kui eelnev on liiga detailne:

A minimalist vector illustration of an abstract figure orchestrating glowing blue cubes into organized stacks, symbolizing Kubernetes container orchestration. Set against a dark background, with clean lines and a futuristic tech aesthetic.
Miks see prompt töötab hästi sinu artikliga:

"Minimalist and futuristic vector illustration": See tagab puhta ja professionaalse stiili, mis sobib tehnoloogia-blogile.
"Abstract, luminous figure, acting as a conductor or architect": See kasutab otse sinu artikli parimaid analoogiaid.
"City's buildings are made of neatly stacked, glowing cubes": See visualiseerib konteinereid kui korrastatud ehitusklotse, mitte kaootilist hunnikut. Värvid (sinine, roheline) on tehnoloogiamaailmas levinud.
"Holographic lines and data streams": See näitab automatiseerimist ja ühenduvust (nagu Service), muutes pildi dünaamiliseks.
"Sense of order being created from complexity": See võtab kokku Kubernetes'e peamise väärtuspakkumise ja sinu artikli eesmärgi.





jätkame nüüd õppimisega! mis järgmiseks oli?



Show thinking
Absoluutselt! Väga hea meel on jätkata.

Meie teekonnal oleme jõudnud punkti, kus meil on:

Töökindel rakendus, mis jookseb Deployment'i abil.
Stabiilne, klastri-sisene võrguaadress sellele rakendusele tänu Service'ile.
Praegune probleem on see, et meie rakendus on kättesaadav ainult klastri seest. Keegi välismaailmast ei pääse sellele ligi. Järgmine loogiline samm on see uks avada.

Selleks on meil Step 4.

Samm 4: Rakenduse Avamine Välismaailmale Ingressiga
Kuidas me saame välise liikluse turvaliselt ja arukalt oma klastris oleva teenuseni suunata? Üks võimalus oleks muuta meie Service'i tüübiks NodePort või LoadBalancer, aga need on sageli kohmakad või kallid, eriti kui sul on kümneid erinevaid teenuseid.

Kõige levinum ja paindlikum viis on kasutada Ingressi.

Mis on Ingress?

Ingress koosneb kahest osast:

Ingress Controller: See on reaalne rakendus (spetsiaalne Pod), mis jookseb sinu klastris. Mõtle sellest kui arukast väravavahist või ruuterist, mis seisab sinu klastri väravas ja võtab vastu kogu sissetuleva liikluse. Populaarseim on NGINX Ingress Controller.
Ingress Resource: See on puhas konfiguratsioon – reeglite kogumik, mille sina kirjutad. Sa ütled sellega väravavahile (Ingress Controllerile), kuidas liiklust suunata. Näiteks: "Kui keegi tuleb aadressilt minuapp.com/login, saada ta sisse 'kasutajate' teenuse juurde. Kui keegi tuleb aadressilt pood.minuapp.com, saada ta 'toodete' teenuse juurde."
Analoogia: Mõtle oma klastrist kui suurest büroohoonest (varem oli see kortermaja, aga nüüd on ärihoone!). Service on nagu iga firma sisene suunakood telefonil. Aga kuidas saab klient tänavalt helistada kindlale firmale? Ta helistab hoone üldnumbrile. Sellele numbrile vastab automaatvastaja (Ingress Controller). Sina oled programmeerinud automaatvastajasse reeglid (Ingress Resource): "kui soovid rääkida 'Müügiosakonnaga', vali 1; kui soovid rääkida 'Raamatupidamisega', vali 2". Automaatvastaja suunab seejärel kõne õigele siseliinile (meie my-nginx-service'ile).

Sinu tegevused Sammus 4:

Loo uuesti eelmise sammu ressursid:
Kuna me eelmise sammu lõpus koristasime, peame oma Deployment'i ja Service'i uuesti looma, et Ingressil oleks, kuhu liiklust suunata. Kui sul on deployment.yaml ja service.yaml alles, siis käivita:

Bash

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
Veendu, et Podid ja Service töötavad: kubectl get pods,svc.

Aktiveeri Ingress Controller Minikube'is:
Minikube'il on kaasas mitu kasulikku lisa (addon), mida saab lihtsalt sisse lülitada. Meie aktiveerime NGINX Ingress Controlleri.

Bash

minikube addons enable ingress
Oodatav tulemus: Näed teadet, et Ingressi lisandmoodul on aktiveeritud. Minikube käivitab nüüd taustal vajalikud Podid Ingress Controlleri jaoks.
Hangi Minikube'i IP-aadress:
Meil on vaja teada, milline on meie klastri väline "üldnumber". Minikube'i puhul on see Minikube'i enda IP-aadress.

Bash

minikube ip
Oodatav tulemus: Saad väljundiks IP-aadressi, näiteks 192.168.49.2. Pane see aadress endale kirja.
Loo Ingressi reegel (Resource):
Nüüd loome reegli, mis ütleb kontrollerile: "kui keegi küsib domeeninime minu-app.info, suuna tema päring edasi teenusele nimega my-nginx-service pordil 80."
Loo fail nimega ingress.yaml:

YAML

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minu-app-ingress
spec:
  rules:
  - host: "minu-app.info"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: my-nginx-service
            port:
              number: 80
Rakenda Ingressi reegel:

Bash

kubectl apply -f ingress.yaml
Simuleeri DNS-kirjet:
Tavaliselt seoksid sa oma domeeni (minu-app.info) DNS-i kaudu oma serveri IP-aadressiga. Kuna meil pole päris domeeni, saame seda oma arvutis simuleerida, muutes hosts faili. See paneb sinu arvuti arvama, et minu-app.info asub sinu Minikube'i IP-aadressil.

Ava /etc/hosts fail administraatori õigustes (näiteks sudo nano /etc/hosts) ja lisa selle lõppu uus rida. Asenda 192.168.49.2 oma Minikube'i IP-aadressiga, mille sa 3. punktis said.

# Lisa see rida /etc/hosts faili
192.168.49.2   minu-app.info
Salvesta fail.

Testi tulemust!
Nüüd on kõik seadistatud. Ava oma terminal (mitte see, mis on Pod'i sees, vaid sinu enda arvuti terminal) ja tee curl päring.

Bash

curl http://minu-app.info
Oodatav tulemus: Sa peaksid nägema "Welcome to nginx!" HTML-lehte!
Mis just juhtus?
a. Sinu arvuti vaatas hosts failist ja saatis päringu minu-app.info kohta sinu Minikube'i IP-le.
b. Päringu võttis vastu Ingress Controller (meie väravavaht).
c. Ingress Controller vaatas oma reegleid (ingress.yaml sisu) ja nägi, et domeeni minu-app.info liiklus tuleb suunata teenusele my-nginx-service.
d. Ingress Controller suunas päringu edasi Service'ile.
e. Service saatis päringu ühele kahest NGINX Pod'ist.
f. NGINX Pod saatis vastuse tagasi mööda sama teed.

Koristamine:
Kui oled katsetamise lõpetanud, saad kõik loodud ressursid kustutada.

Bash

kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
kubectl delete -f ingress.yaml
Ära unusta eemaldada rida, mille lisasid /etc/hosts faili!

Palju õnne! See oli väga oluline samm. Sa oled nüüd võimeline oma klastris jooksvaid rakendusi turvaliselt ja paindlikult välismaailmale avama. See on muster, mida kasutatakse väga paljudes reaalsetes Kubernetes'e paigaldustes.

Kui oled valmis, võime edasi liikuda ja vaadata, kuidas oma rakendustele konfiguratsiooni ja salajasi andmeid (nagu paroolid ja API võtmed) hallata.







Allpool on meie Kuberenetese õppimise artiklisarja esimene artikkel. Soovin, et seda täiustaksid:

- aita mul artiklile SEO ja conversioni jaoks hea pealkiri mõelda

- artikli alguses on frontmatter, ära seda puutu (va. uus pealkiri ja brief description, kui sul on häid ideid)

- kirjuta sissejuhatus, kus räägid artiklite sarjast ja paned selle artikli konteksti. koosta mulle curriculum Kuberenetese õppimise jaoks ja kirjelda seda sissejuhatuses

- ära kasuta pealkirjades läbivaid suurtähti

- kohad artiklis, kus on bash käskude käivitamine, too ära ka oodatav output, et õppur saaks võrrelda

- igal pool võiks olla rohkem konteksti, et kus me protsessis parasjagu oleme



---

title: "Sissejuhatus Kubernetesesse: Sinu esimene rakendus"           # Required: Post title

author: "Pilvio Admin Team"                   # Required: Author name

publishedAt: "16. juuni 2025"        # Required: Estonian format date

category: "Õpetused"                  # Required: Post category

tags: ["kubernetes", "devops"]          # Required: Array of tags

excerpt: "Selles postituses juhendame sind läbi Kubernetes'e põhikontseptsioonide, alustades sinu esimese rakenduse paigaldamisest kuni selle kättesaadavaks tegemiseni klastri sees.

"         # Optional: Custom excerpt (auto-generated if not provided)

imageUrl: "/media/kubernetes_intro_1.png" # Optional: Featured image URL

slug: "sissejuhatus-kubernetesesse-1"                    # Optional: Custom URL slug (auto-generated from title if not provided)

---

### Sissejuhatus Kubernetesesse: Sinu esimene rakendus



Tere tulemast meie blogiseeriasse, mis on pühendatud Kubernetesesse sisseelamisele! See on esimene postitus, mis aitab sul astuda esimesi samme konteinerite orkestreerimismaailmas. Kubernetes, tihti lühendatud kui K8s, on võimas avatud lähtekoodiga platvorm, mis automatiseerib konteineriseeritud rakenduste paigaldamist, skaleerimist ja haldamist.



Mõtle Kubernetesest kui sümfooniaorkestri dirigendist. Sinu rakendused on muusikud. Dockeriga saad sa öelda igale muusikule (konteinerile), mida mängida. Aga Kubernetes on dirigent, kes juhatab kogu orkestrit: ta tagab, et kõik alustavad õigel ajal, mängivad harmoonias, asendab muusiku, kui see peaks ära väsima, ja toob juurde uusi viiuleid, kui muusika peab muutuma võimsamaks.



Selles postituses juhendame sind läbi Kubernetes'e põhikontseptsioonide, alustades sinu esimese rakenduse paigaldamisest kuni selle kättesaadavaks tegemiseni klastri sees.



#### Eeldused



Enne alustamist eeldame, et sul on:



- Põhiteadmised Dockerist ja konteineritest. Sa mõistad, mis on tõmmis (`image`) ja mis on konteiner.

- Soov õppida ja katsetada käsureal.



Selles juhendis kasutame kohaliku arenduskeskkonna jaoks Minikube'i. **Siin Pilvios oleme kirjutanud eraldi põhjaliku juhendi**, kuidas seadistada Minikube koos Terraformiga meie platvormil. Palun järgi neid juhiseid, et oma Kubernetes'e testkeskkond püsti panna: [Kuidas paigaldada Minikube Pilvios Terraformiga](https://blog.pilvio.com/blog/minikube-terraform).



Kui oled sellega valmis, liigume edasi.



### Tööriistade Mõistmine: `kubectl` ja Minikube



Enne kui loome oma esimese rakenduse, on ülioluline mõista kahte põhilist tööriista, mille just seadistasid: `kubectl` ja Minikube.



#### `kubectl`: Sinu Kaugjuhtimispult



`kubectl` (hääldatakse "kjuub-kontroll") on sinu peamine ja kõige olulisem tööriist Kubernetes'e klastriga suhtlemiseks.



**Analoogia:** Mõtle `kubectl`-ist kui oma teleri kaugjuhtimispuldist. Pult ise ei tea, kuidas teleri sisemine elektroonika pilti tekitab. Sa lihtsalt vajutad nuppu "Kanal 5" ja teler teab, mida teha. `kubectl` on samasugune – sa annad käsu (`kubectl get pods`) ja ei pea muretsema, kuidas klaster selle info täpselt hangib. Sa suhtled ainult puldiga, mitte ei hakka teleri tagapaneeli lahti kruvima.



Kuidas see töötab?



Iga kord, kui sa käivitad käsu, teeb kubectl järgmist:



1. **Loeb konfiguratsiooni:** Sinu kodukataloogis on peidetud fail `.kube/config`. See on nagu puldi ja teleri "paaristamise" info. Sinna on kirja pandud sinu Minikube'i klastri unikaalne aadress ja turvavõtmed, mis tõestavad, et just sinul on õigus sellele klastrile käske anda.

2. **Saadab API päringu:** `kubectl` vormistab sinu käsu standardseks API-päringuks ja saadab selle üle võrgu sinu klastri "juhtimiskeskusesse", mida nimetatakse **API Serveriks**. See on ainus värav, mille kaudu klastriga suheldakse.

3. **Näitab vastust:** API Server töötleb päringu, kogub kokku vajaliku info ja saadab selle puldile tagasi. `kubectl` vormindab selle sinu jaoks kenasti terminalis loetavaks.



#### Minikube: Sinu Isiklik Kubernetes'e Klaster



Minikube'i eesmärk on anda sulle täisfunktsionaalne, aga lihtsustatud Kubernetes'e klaster, mis jookseb sinu enda arvutis.



**Analoogia:** Kujuta ette, et tahad õppida linna planeerimist. Selle asemel, et ehitada päris linna (mis oleks tohutult kallis ja keeruline), ehitad sa oma tuppa väikesele lauale miniatuurse Lego-linna. Selles Lego-linnas on olemas kõik olulised hooned: raekoda, postkontor, arhiiv. Minikube ongi sinu Lego-linn. Päris Kubernetes'e klaster on päris linn, aga õppimiseks sobib Lego-mudel ideaalselt.



Kuidas see töötab?



Kui sa käivitasid minikube start, ehitas see sinu Lego-linna valmis, luues ühe Docker'i konteineri, mille sees pandi tööle kõik linna olulised teenused (Kubernetes'e juhtplaan ehk Control Plane):



- **API Server:** See on linna raekoda ja postkontor ühes. Kõik käsud (`kubectl` päringud) saabuvad siia.

- **etcd:** See on linna arhiiv. Ülimalt turvaline ja töökindel andmebaas, kus hoitakse kirjas absoluutselt kõike, mis linnas toimub: millised majad on ehitatud, kellele need kuuluvad jne.

- **Scheduler:** See on linnaarhitekt. Kui keegi tahab ehitada uut maja (Pod'i), siis arhitekt vaatab plaani ja otsustab, millisele vabale krundile (sõlmele ehk *node*'ile) see maja ehitatakse. Minikube'i puhul on meil ainult üks krunt.



Nüüd, kui me mõistame oma tööriistu, oleme valmis ehitama oma esimese maja.



### Samm 1: Sinu esimene Pod



Kõige väiksem ja fundamentaalsem ehituskivi Kuberneteses on **Pod**.



**Analoogia:** Kui Kubernetes on linn, siis **Pod on korter** kortermajas. Korter ise ei tee midagi, aga see pakub elamiseks vajalikke tingimusi: seinu, elektrit, vett ja ühte kindlat postiaadressi. Need tingimused ongi konteineri jaoks vajalik jagatud võrguruum ja salvestusruum. Tavaliselt elab korteris üks elanik (sinu rakenduse konteiner). Mõnikord võib seal olla ka väike abiline, kes elab panipaigas (see oleks *sidecar*-konteiner).



Loome deklaratiivselt oma esimese korteri. See tähendab, et me ei anna käsku "ehita korter!", vaid anname ehitajale (Kubernetesele) detailse joonise (`.yaml` fail) ja ütleme: "tee nii, et reaalsus vastaks sellele joonisele."



Loo fail nimega `pod.yaml`:



YAML



```

apiVersion: v1

kind: Pod

metadata:

  name: minu-esimene-pod

  labels:

    app: veebiserver

spec:

  containers:

  - name: nginx-konteiner

    image: nginx

```



**YAML-faili lahkamine:**



- `apiVersion: v1`: "Me räägime API Serveriga keele versioonis 1."



- `kind: Pod`: "Joonise tüüp on 'Korter' (Pod)."



- `metadata`: "Korteri andmed."

  - `name: minu-esimene-pod`: "Anname korterile nime (uksesildi)."

  - `labels: app: veebiserver`: "Värvime korteri ukse punaseks (silt `app: veebiserver`). See on ülioluline, sest hiljem saame öelda: 'leia kõik punase uksega korterid'."



- `spec`: "Korteri detailne siseplaan."

  - `containers:`: "Siin on nimekiri elanikest (konteineritest), kes siin korteris elavad."

  - `- name: nginx-konteiner`: "Elaniku nimi on 'nginx-konteiner'."

  - `image: nginx`: "See elanik on loodud 'nginx' tõmmise (DNA) põhjal."



Nüüd anname joonise Kubernetes'ele:



Bash



```

kubectl apply -f pod.yaml

```



Kontrollime, kas korter sai valmis ja elanik kolis sisse:



Bash



```

kubectl get pods

```



Näeme, et `minu-esimene-pod` on staatuses `Running`. Nüüd koristame selle ära, sest me õppisime just, et Podid on surelikud. Me ei taha hallata üksikuid kortereid, vaid tervet kortermaja.



Bash



```

kubectl delete -f pod.yaml

```



### Samm 2: Usaldusväärne rakendus Deploymentiga



Pod on liiga habras. Kui rakendus jookseb kokku, on Pod surnud. Me vajame kedagi, kes automaatselt uue asemele loob. See keegi on **Deployment**.



**Analoogia:** Kui Pod on korter, siis **Deployment on kortermaja haldur**. Sa ei ütle haldurile, et "ehita üks korter". Sa annad talle maja joonise ja reegli: "Selles majas peab ALATI olema kaks identset, elanikega asustatud korterit (`replicas: 2`)." Kui ühes korteris toimub uputus (Pod jookseb kokku), siis haldur märkab seda, sulgeb selle korteri ja ehitab koheselt joonise alusel uue asemele. Sina ei pea sekkuma.



Loome haldurile tööjuhendi failis `deployment.yaml`:







YAML



```

apiVersion: apps/v1

kind: Deployment

metadata:

  name: nginx-deployment

spec:

  replicas: 2

  selector:

    matchLabels:

      app: veebiserver

  template:

    metadata:

      labels:

        app: veebiserver

    spec:

      containers:

      - name: nginx

        image: nginx

        ports:

        - containerPort: 80

```



**Tööjuhendi lahkamine:**



- `kind: Deployment`: "See on tööjuhend 'Kortermaja Haldurile'."

- `replicas: 2`: "Halduri põhireegel: alati hoia töös 2 identset korterit."

- `selector`: "Kuidas haldur teab, millised korterid on tema omad? Ta otsib neid, millel on uksel silt `app: veebiserver`." See on tema vastutusala.

- `template`: "See on detailne korteri joonis, mida haldur kasutab IGA KORD, kui on vaja uus korter ehitada. Pane tähele, et joonisel endal peab olema sama silt (`labels: app: veebiserver`), et haldur saaks pärast ehitamist aru, et see on tema hallatav korter."



Anname tööjuhendi haldurile:



Bash



```

kubectl apply -f deployment.yaml

```



Kontrollime halduri tööd:



Bash



```

kubectl get pods

```



Näeme kahte NGINX Pod'i, mille haldur on loonud. Nüüd on meil töökindel süsteem!



### Samm 3: Rakenduse kättesaadavaks tegemine Service'iga



Meil on nüüd kaks korterit, aga nende uksekoodid (IP-aadressid) on ajutised. Kui haldur ehitab uue korteri, saab see uue uksekoodi. Kuidas saaksime saata kirja "NGINX elanikele", kui me ei tea nende täpset aadressi? Lahendus on **Service**.



**Analoogia:** **Service on kortermaja fuajees asuv postkontor või infopunkt**. Sellel postkontoril on üks, püsiv ja avalik aadress (selle `ClusterIP` ja DNS-nimi). Kõik kirjad (võrguliiklus) saadetakse sellele aadressile. Postkontori töötaja (`selector`) vaatab kirja ja teab täpselt, millistel ustel on silt `app: veebiserver`. Ta võtab kirja ja viib selle ühele neist elanikest. Kui elanikke on mitu, jaotab ta kirjad nende vahel võrdselt ära (see ongi koormuse jaotamine ehk *load balancing*).



Loome postkontorile reeglid failis `service.yaml`:



YAML



```

apiVersion: v1

kind: Service

metadata:

  name: minu-nginx-service

spec:

  type: ClusterIP

  selector:

    app: veebiserver

  ports:

    - protocol: TCP

      port: 80

      targetPort: 80

```



**Postkontori reeglite lahkamine:**



- `type: ClusterIP`: "See postkontor teenindab ainult maja-siseseid kirju (liiklus on avatud ainult klastri sees)."



- `selector: app: veebiserver`: "Postitöötaja reegel: vii kirjad edasi ainult neile korteritele, mille uksel on silt `app: veebiserver`." See ühendab postkontori õigete korteritega.



- `ports`: "Kuidas kirju vastu võetakse ja edastatakse."

  - `port: 80`: "Kirja vastuvõtmise postkasti number fuajees on 80."

  - `targetPort: 80`: "Korteri ukse küljes oleva postipilu number, kuhu kiri tuleb panna, on samuti 80."



Avame postkontori:



Bash



```

kubectl apply -f service.yaml

```



Kontrollime, kas postkontor töötab:



Bash



```

kubectl get service minu-nginx-service

```



Väljund näitab, et meie teenusel on nüüd stabiilne, maja-sisene aadress (`CLUSTER-IP`).



### Kokkuvõte



Palju õnne! Sa oled edukalt loonud oma esimese rakenduse Kuberneteses, kasutades selleks professionaalseid ja töökindlaid meetodeid.



Mida sa täna õppisid läbi analoogiate:



- Sa mõistad põhilisi tööriistu: **`kubectl`** on sinu pult ja **Minikube** on sinu isiklik Lego-linn.

- Sa tead, mis on Kubernetes'e ehituskivid: **Pod** on korter, **Deployment** on tark maja haldur ja **Service** on maja postkontor.

- Sa oskad luua töökindla ja skaleeritava rakenduse, mis suudab end ise parandada.



Hetkel on meie rakendus kättesaadav ainult klastri seest (maja-siseselt). Meie seeria järgmises postituses vaatame, kuidas oma rakendus turvaliselt ka välismaailmale avada, ehitades maja ette korraliku sissepääsu – selleks kasutame **Ingress**'i. Püsige lainel!



Hästi, siin on sinu artikli täiustatud ja rikastatud versioon, mis arvestab kõiki su soove.

---
title: "Praktiline sissejuhatus Kubernetesesse: sinu esimene rakendus (1. osa)"
author: "Pilvio Admin Team"
publishedAt: "16. juuni 2025"
category: "Õpetused"
tags: ["kubernetes", "devops", "alustamine", "õpetus"]
excerpt: "Alusta oma teekonda Kubernetesega! See on meie artiklisarja esimene osa, kus õpid praktiliste näidete varal selgeks Pod'i, Deployment'i ja Service'i põhimõtted."
imageUrl: "/media/kubernetes_intro_1.png"
slug: "praktiline-sissejuhatus-kubernetesesse-1"
---
Tere tulemast Kubernetes'e maailma!
Tere tulemast meie uude artiklisarja "Nullist kangelaseks Kubernetesega"! See sari on mõeldud kõigile, kes soovivad samm-sammult ja praktiliste näidete varal selgeks õppida Kubernetes'e – tänapäeva ühe olulisima konteinerite orkestreerimise platvormi.

Oled sa arendaja, kes tahab oma rakendusi paremini hallata, või süsteemiadministraator, kes ehitab tulevikukindlaid lahendusi, see sari on just sulle. Me ei uputa sind kuiva teooriasse, vaid ehitame koos, klots klotsi haaval, toimiva süsteemi, seletades iga sammu juures lahti, miks me midagi teeme.

Selleks, et teekond oleks selge, oleme koostanud järgmise õppekava, millega liigume algaja tasemelt enesekindla kasutaja tasemeni:

1. osa: Aluste loomine (see artikkel)

Mis on Kubernetes ja miks seda vaja on?
Tööriistade (kubectl, Minikube) seadistamine ja mõistmine.
Põhikontseptsioonid: Pod, Deployment ja Service. Eesmärk on saada lihtne rakendus klastri-siseselt tööle.
2. osa: Rakenduse avalikustamine ja konfigureerimine

Rakenduse turvaline avamine välismaailmale Ingress'i abil.
Rakenduse seadete ja paroolide haldamine ConfigMap'ide ja Secret'itega.
3. osa: Andmete püsivus ja olekuga rakendused

Kuidas hallata andmebaase ja teisi olekuga rakendusi? Tutvume PersistentVolume'ite ja StatefulSet'idega.
4. osa: Jälgimine ja automatiseerimine

Kuidas oma klastri ja rakenduste tervisel silma peal hoida?
Sissejuhatus CI/CD torujuhtmetesse – kuidas automatiseerida rakenduse paigaldamist Kubernetesesse.
Selles esimeses osas paneme paika vundamendi. Alustame!

Tööriistade ettevalmistus ja mõistmine
Kus me oleme? Enne ehitama asumist peame oma tööriistad valmis panema ja nendega tuttavaks saama. See on meie teekonna "Samm 0".

Eeldused
Enne alustamist eeldame, et sul on:

Põhiteadmised Dockerist ja konteineritest. Sa mõistad, mis on tõmmis (image) ja mis on konteiner.
Soov õppida ja katsetada käsureal.
Selles juhendis kasutame kohaliku arenduskeskkonna jaoks Minikube'i. Siin Pilvios oleme kirjutanud eraldi põhjaliku juhendi, kuidas seadistada Minikube koos Terraformiga meie platvormil. Palun järgi neid juhiseid, et oma Kubernetes'e testkeskkond püsti panna: Kuidas paigaldada Minikube Pilvios Terraformiga.

Kui kubectl ja Minikube on paigaldatud, liigume edasi nende hingeeluga tutvumisele.

kubectl ja Minikube: sinu pult ja mänguväljak
Kus me oleme? Mõistame oma kahte kõige olulisemat tööriista. kubectl on sinu "pult" klastriga suhtlemiseks ja Minikube on sinu isiklik "Lego-linn" ehk testklaster.

kubectl: sinu kaugjuhtimispult
kubectl (hääldatakse "kjuub-kontroll") on sinu peamine ja kõige olulisem tööriist Kubernetes'e klastriga suhtlemiseks.

Analoogia: Mõtle kubectl-ist kui oma teleri kaugjuhtimispuldist. Sa lihtsalt vajutad nuppu "Kanal 5" ja teler teab, mida teha. kubectl on samasugune – sa annad käsu (kubectl get pods) ja ei pea muretsema, kuidas klaster selle info täpselt hangib.

Iga kord, kui sa käivitad käsu, loeb kubectl sinu kodukataloogist .kube/config faili, kus on kirjas klastri aadress ja turvavõtmed. Seejärel vormistab ta sinu käsu API-päringuks ja saadab selle klastri "juhtimiskeskusesse", mida nimetatakse API Serveriks.

Minikube: sinu isiklik Kubernetes'e klaster
Minikube'i eesmärk on anda sulle täisfunktsionaalne, aga lihtsustatud Kubernetes'e klaster, mis jookseb sinu enda arvutis.

Analoogia: Mõtle sellest kui miniatuursest Lego-linnast oma laual. Päris Kubernetes'e klaster on nagu päris linn, aga õppimiseks sobib Lego-mudel ideaalselt. Selles Lego-linnas on olemas kõik olulised hooned:

API Server: Linna raekoda, kuhu kõik käsud saabuvad.
etcd: Linna turvaline arhiiv, kus hoitakse kogu infot.
Scheduler: Linnaarhitekt, kes otsustab, kuhu uus maja (Pod) ehitada.
Nüüd, kui me mõistame oma tööriistu, oleme valmis ehitama oma esimese maja.

Samm 1: sinu esimene Pod
Kus me oleme? Loome kõige lihtsama ja väiksema ehitusploki Kuberneteses – Pod'i. See on meie esimene praktiline samm klastri sees.

Analoogia: Kui Kubernetes on linn, siis Pod on korter. Korter pakub elamiseks vajalikke tingimusi (seinu, elektrit, vett), milleks on konteineri jaoks jagatud võrgu- ja salvestusruum.

Loome deklaratiivselt oma esimese korteri, andes Kubernetes'ele detailse joonise (.yaml fail).

Loo oma arvutisse fail nimega pod.yaml:

YAML

apiVersion: v1
kind: Pod
metadata:
  name: minu-esimene-pod
  labels:
    app: veebiserver
spec:
  containers:
  - name: nginx-konteiner
    image: nginx
YAML-faili lahkamine:

kind: Pod: Määrame, et loome "korteri".
metadata: Lisame nimesildi (name) ja värvime ukse punaseks (labels), et selle hiljem üles leiaksime.
spec: Kirjeldame korteri sisu – ühe elaniku (container), kes on loodud nginx tõmmise (DNA) põhjal.
Nüüd anname joonise Kubernetes'ele käsuga kubectl apply:

Bash

kubectl apply -f pod.yaml
Oodatav tulemus:

pod/minu-esimene-pod created
Kontrollime, kas korter sai valmis ja elanik kolis sisse:

Bash

kubectl get pods
Oodatav tulemus:

NAME               READY   STATUS    RESTARTS   AGE
minu-esimene-pod   1/1     Running   0          30s
Näeme, et Pod on staatuses Running. Kuna aga Pod'id on surelikud ja me tahame ehitada töökindlamaid süsteeme, siis koristame selle üksiku korteri kohe ära.

Bash

kubectl delete -f pod.yaml
Oodatav tulemus:

pod "minu-esimene-pod" deleted
Samm 2: töökindel rakendus Deployment'iga
Kus me oleme? Liigume edasi üksikult "korterilt" terve "kortermaja" haldamise juurde. Loome Deployment'i, mis tagab, et meie rakendus on alati töös.

Analoogia: Kui Pod on korter, siis Deployment on kortermaja haldur. Sa annad talle reegli: "Selles majas peab ALATI olema kaks identset korterit (replicas: 2)." Kui üks korter läheb katki, ehitab haldur kohe uue asemele.

Loome haldurile tööjuhendi failis deployment.yaml:

YAML

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: veebiserver
  template:
    metadata:
      labels:
        app: veebiserver
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
Tööjuhendi lahkamine:

replicas: 2: Halduri põhireegel – hoia töös 2 identset Pod'i.
selector: Kuidas haldur teab, millised Pod'id on tema omad? Ta otsib neid, millel on silt app: veebiserver.
template: See on detailne korteri joonis, mille alusel haldur uusi Pod'e ehitab.
Anname tööjuhendi haldurile:

Bash

kubectl apply -f deployment.yaml
Oodatav tulemus:

deployment.apps/nginx-deployment created
Kontrollime halduri tööd:

Bash

kubectl get pods
Oodatav tulemus:

NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7848d4b8d5-7c5jc   1/1     Running   0          25s
nginx-deployment-7848d4b8d5-x5zvm   1/1     Running   0          25s
Näeme kahte NGINX Pod'i, mille haldur on loonud. Nüüd on meil töökindel süsteem!

Samm 3: rakenduse ühendamine Service'iga
Kus me oleme? Meil on töökindel rakendus, aga sellel puudub stabiilne aadress klastri sees. Loome Service'i, et anda meie rakendusele püsiv võrguidentiteet.

Analoogia: Service on kortermaja fuajees asuv postkontor. Sellel on üks, püsiv aadress (ClusterIP). Kõik kirjad (võrguliiklus) saadetakse sinna ja postkontori töötaja (selector) suunab need õigele elanikule (Pod'ile).

Loome postkontorile reeglid failis service.yaml:

YAML

apiVersion: v1
kind: Service
metadata:
  name: minu-nginx-service
spec:
  type: ClusterIP
  selector:
    app: veebiserver
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
Postkontori reeglite lahkamine:

type: ClusterIP: See postkontor teenindab ainult maja-siseseid kirju.
selector: Postitöötaja reegel – suuna kirjad edasi Pod'idele, mille silt on app: veebiserver.
ports: Määrame, et postkontor võtab liiklust vastu pordil 80 ja suunab selle Pod'i pordile 80.
Avame postkontori:

Bash

kubectl apply -f service.yaml
Oodatav tulemus:

service/minu-nginx-service created
Kontrollime, kas postkontor töötab:

Bash

kubectl get service minu-nginx-service
Oodatav tulemus:

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
minu-nginx-service   ClusterIP   10.108.111.44   <none>        80/TCP    15s
Väljund näitab, et meie teenusel on nüüd stabiilne, maja-sisene aadress (CLUSTER-IP).

Kokkuvõte ja järgmised sammud
Kus me oleme? Oleme esimese osa lõpus. Sa oled edukalt loonud oma esimese rakenduse Kuberneteses!

Mida sa täna õppisid:

Mõistad põhilisi tööriistu: kubectl on sinu pult ja Minikube on sinu Lego-linn.
Tead Kubernetes'e põhilisi ehituskive: Pod on korter, Deployment on tark maja haldur ja Service on maja postkontor.
Oskad luua töökindla ja skaleeritava rakenduse, mis on kättesaadav klastri sees.
Hetkel on meie rakendus veel välismaailma eest peidus. Meie sarja teises osas ehitame maja ette korraliku sissepääsu, kasutades Ingress'i, et rakendus turvaliselt avalikuks teha. Samuti vaatame, kuidas hallata rakenduse seadeid ja paroole.

Püsi lainel!












Deep Research

Canvas

Your pilw.io chats aren’t used to improve our models. Gemini can make mistakes, including about people, so double-check it. Your privacy & Gemini

