### Goals

- The trainee will understand k8s's components and architecture

  

### Tasks

- Read the following book: [Kubernetes in action](https://drive.google.com/file/d/1XVZp5QlZh3R9R--sPchcUV7kGlOUz6g4/view?usp=sharing)

  It is recommended to deploy minikube on your own VM and get hands-on experience while you are reading the book :)

  **Skip the following chapters**:
    - 15.3
    - 17.4
    - 17.5
    - 18.2 and onwards, except Appendix A


**Additional Sources**
- [Certified Kubernetes Administrator course from Udemy](https://drive.google.com/drive/folders/1GNXztUdtESDTt5uefyV7n5NpofaxLPn4?usp=sharing)

- [The Kubernetes Book by Nigel Poulton (a shorter version)](https://drive.google.com/file/d/1tnHb9ijciKs6cI07XJrg4TUrCWjNtaUu/view)

### Questions
**Answer the following questions while reading the book:**

  Pay attantion the questions are not in the exact order. You should continue reading and only if you finished the book search the missing answers. 
  Are all questions answered? You are ready to go.
  
**Introducing Kubernetes**
  - What are some of the benefits of microservices applications over monolith applications?
  - What are the challenges that come with microservices architecture?
  - What necessity has led Google to develop Kubernetes?

**Pods**
  - Why would we need to run a few containers in one pod?
  - If containers are supposed to be isolated environments, how can they share resources inside a pod?
  - Why should you always gravitate toward running containers in separate pods?
  - Why do we need to label pods?
  - Why shouldn’t pods be created directly?
  - What are Kubernetes namespaces?
  - How does Kubernetes ensure containers inside pods are always running?
  - Name a few ways to manage pods and shortly explain each one.

**Services**
  - Why shouldn’t we access pods directly? 
  - Why do we need services?
  - Name 3 ways to access a service from outside the cluster and explain each one.
  - What is a Headless Service?

**🚀Exercise**
  - Deploy a simple web application (e.g., `us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0`) and access it from outside the cluster.
  Use our dev GKE cluster

**Volumes**
  - What are Kubernetes volumes?
  - If it’s possible to persist data with regular external storage volumes, why would we need PersistentVolumes? 
  - How applications receive persistent storage according to their needs?
  - What are the benefits of dynamically provisioning PersistentVolumes?

**ConfigMaps & Secrets**
  - Why are ConfigMaps useful?
  - What are the ways to pass ConfigMaps to containers in a pod?
  - When should I use a ConfigMap and when should I use a Secret?
  - What is a default-token Secret?

**Deployments**
  - What are Deployments? 
  - How do rolling updates work?
  - How are rollbacks possible?
  
**StatefulSets**
  - When will we use StatefulSets?
  - How StatefulSets create and scale stateful pods? Explain about the ordinal index
  - How StatefulSets manage volumes?
  - How StatefulSets handle failures?
  - Why are headless services useful when using StatefulSets?

**K8s internals**
  - What components make up the control plane and what do each of them do?
  - What components run on the worker nodes? What do each of them do?
  - How do all of these components communicate?
  - How does the scheduler choose what node is the best for a deployment?
  - What are the controller types?
  - describe the chain of events from the moment you create a deployment to the moment the pods are actually created
  - What does the pod construct add to the containers running inside it?
  - How do Pod to Pod communication works in the same node? ve (virtual ethernet)
  - How do Pod to Pod communication works, both on a different node? 
  - How Pod to Service communication works? kube-proxy, iptables, chains
  - How External traffic from cluster to pod communication works? ingress
  - How does the kube-proxy perform load balancing across pods?
  - why the number of etcd instances should be an odd-number? 
  - Describe the flow of api request in Kubernetes (auth, RBAC, admissionController)
  - Extra: Is there a kubelet on the master?

**Securing the kubernetes api server**
  - What is a ServiceAccount?
  - What are the RBAC components?
  - What happens when you bind a Role with a ClusterRoleBinding?
  - What happens when you bind a ClusterRole with a RoleBinding?

**Securing cluster nodes**
  - What was PodSecurityPolicy, and why was it removed in Kubernetes 1.25?
  - Read about Pod Security Admission and the Pod Security Standards (privileged, baseline, restricted)
  - How do you enforce a Pod Security Standard on a namespace?
  - Managing pod’s computational resources:
  - how can you limit the pod’s resources (requests, limits ,QOS etc)
  - How does the cpu request affect the system?
  - Why do we need resource requests and limits?
  - What is the difference between the ResourceQuota object and the LimitRange object?

**Automatic scaling of pods and cluster nodes**
  - What are horizontal and vertical scaling and why do we need them?
  - How can you perform horizontal scaling in k8s?

**Advanced scheduling**
  - how can you ensure pods are not scheduled to certain nodes?
  - how can you ensure pods are only scheduled to specific nodes? 
  - How can you keep certain pods away from each other?

**Best practices for developing apps**
  - describe a typical application in kubernetes (all resources that make up the app)
  - What are init containers?
  - What is CustomResource? When would you use it?
  - What is CustomResourceDefinition?
  - What is a custom controller and why do we need it?