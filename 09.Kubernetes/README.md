### Goals

- The trainee will understand k8s's components and architecture

  

### Tasks

- Read the following book: *Kubernetes in Action, Second Edition* by Marko Lukša and Kevin Conner (Manning): https://www.manning.com/books/kubernetes-in-action-second-edition

  It is recommended to deploy minikube on your own VM and get hands-on experience while you are reading the book :)

  **Skip the following chapters** (these numbers were written for the first edition; re-map them to the second edition's table of contents before trainees start):
    - 15.3
    - 17.4
    - 17.5
    - 18.2 and onwards, except Appendix A


**Minikube checklist (do it while you read)**

Start a cluster with `minikube start`, and check it with `kubectl get nodes`. For each item, run the commands, check the result, and write one sentence about what you saw. Map each item to the book chapter that covers it.

1. **Pods:** run a pod with `kubectl run`, read its logs, run a command inside it with `kubectl exec`, then delete it.
   *Done when:* the pod is gone, and nothing recreates it.
2. **Deployments and ReplicaSets:** create a Deployment with 3 replicas. Delete one pod.
   *Done when:* a new pod appears within seconds, and `kubectl get rs` shows why.
3. **Rollouts:** change the image of the Deployment, then run `kubectl rollout undo`.
   *Done when:* you can explain what happened to the old and new ReplicaSets.
4. **Services:** expose the Deployment as a ClusterIP Service. Call it from a temporary pod (`kubectl run tmp --rm -it --image=busybox -- wget -qO- <service>`).
   *Done when:* you see your app's response, and `kubectl get endpoints` lists the pod IPs.
5. **Labels and selectors:** change the Service selector so it matches nothing. Then fix it.
   *Done when:* the endpoints list is empty while it's broken, and full again after the fix.
6. **ConfigMaps and Secrets:** create one of each, mount one as an environment variable and the other as a file. Change the values.
   *Done when:* you can say which values updated in the running pod, and which didn't.
7. **Storage:** share an `emptyDir` between two containers in one pod. Create a PersistentVolumeClaim, write a file to it, delete the pod, and recreate it.
   *Done when:* the file is still there, and you can say why an `emptyDir` file would not be.
8. **Probes and resources:** add a readiness probe that fails, then set CPU and memory requests.
   *Done when:* the pod shows `Ready 0/1`, the Service has no endpoints, and `kubectl describe node` shows your requests in "Allocated resources".
9. **Scheduling:** set a `nodeSelector` with a label that no node has.
   *Done when:* the pod is `Pending`, and `kubectl describe pod` shows the reason in the events.
10. **Namespaces and RBAC:** create a namespace, a ServiceAccount, a Role and a RoleBinding. Check them with `kubectl auth can-i --as=system:serviceaccount:<ns>:<name> <verb> <resource>`.
    *Done when:* the answers match the Role you wrote.
11. **Gateway API:** do the Gateway API exercise below.

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

**Exposing services: Gateway API**
  - How does traffic from outside the cluster reach a Pod? Name every hop (external load balancer, node, Gateway, HTTPRoute, Service, Pod)
  - Why not give every Service its own `LoadBalancer`? What does it cost, in money and in IP addresses?
  - What is the **Gateway API**, and why is it the recommended way to expose HTTP and other traffic in Kubernetes? Read the official Gateway API page
  - Which problems of the older Ingress API does the Gateway API solve? (Think about: who owns what, portable features, protocols other than HTTP, cross-namespace routing)
  - Explain the Gateway API resources and who owns each one:
    - `GatewayClass` - who creates it, and what does it point to?
    - `Gateway` - which listeners, ports and TLS certificates does it define?
    - `HTTPRoute` - how does it attach to a Gateway (`parentRefs`), and how does it route by host, path and header?
    - What other route types exist (e.g. `GRPCRoute`, `TCPRoute`)? Which of them are stable?
  - What is an **implementation** (a Gateway controller)? Name 3 implementations. Which GatewayClass names does GKE create?
  - What is the difference between an external and an internal Gateway? What does an internal Gateway need in the subnet (connect this to the **proxy-only subnet** in 05.NetworkingAndDNS)?
  - Write an HTTPRoute that sends `/api` to one Service and everything else to another. Which match type did you use, and why?
  - You have an old Ingress manifest with host and path rules. Convert it to a `Gateway` and an `HTTPRoute`. Which fields moved where, and what did you have to add?
  - Your team has legacy Ingress resources. How would you migrate them without downtime? What would you check first?
  - **🚀 Exercise:** on minikube, install the Gateway API CRDs and a Gateway API implementation (e.g. Envoy Gateway or NGINX Gateway Fabric). Deploy two small web apps. Expose both with one `Gateway` and two `HTTPRoute`s: one by host name, one by path (`/app1`, `/app2`).
  - **🚀 Exercise (bonus):** write a small Ingress manifest for the same two apps, convert it to Gateway API, and explain every change.

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
  - How does external traffic reach a Pod? Follow it through a Gateway, an HTTPRoute and a Service
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