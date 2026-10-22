# Helm

### Goals
- The trainee will get familiar with Helm and Helm Charts

### Tasks
- Install helm cli tool and experiment with helm
- Use helm cli to create a helm chart and see what it looks like
- Answer the following questions:
    - What is helm and what is it used for?
    - What is a Helm chart's directory structure?
    - What is Chart.yaml?
    - What are the benefits of using Helm Charts?
    - What templating language is Helm using?
    - How can you customize a helm chart?
    - What is the '.'?
    - What special values do you have access to when templating?
    - What are named templates?
    - what is _helpers.tpl file? Why does it start with an underscore?
    - What effect does the "{{-" simbol has on your template?
    - What are subcharts?
    - Create a helm chart which uses some bitnami helm chart as a dependency
    - How can I control values passed to a subchart?
    - What effect do variables under `.Values.global` have?
    - How can I use two value files with helm cli?
    - What is the equivalent single value file to using these two files (helm template --values A.values.yaml --values B.values.yaml )? Try working it out and write it before checking with helm.
    #### A.values.yaml
    ```yaml
    yoav:
      instrument: piano
      address: frishman
      hobbies:
        - 'riding bike'
        - 'playing music'
        - knives
      favoriteFood: 'lint 90% dark chocolate'
    notebookClosed: true
    ```
    #### B.values.yaml
    ```yaml
    yoav:
      nickName: yoavi
      hobbies:
        - 'riding bike'
        - 'walking bare foot'
        - 'getting released' 
      favoriteFood: hada
    notebookClosed: false
    guitarCount: 2
    ``` 
    - Read the helm docs about conventions and best practices

    - ArgoCD
        - Read about GitOps
        - What are the principles of GitOps? What are the advantages?
        - Read about ArgoCD
        - How does ArgoCD achieve GitOps for k8s applications?
        - Read about ArgoCD's Application and ApplicationSet resources. Why are they useful?
        - What is a generator in applicationset? What are the different types?
# 🚀 ArgoCD Exercise
- Do the following course: https://academy.akuity.io/courses/gitops-argocd-intro

# 🚀 DevOps Exercise

🕒 **Estimated Time**: 1 full day  
🧪 **Kubernetes Cluster Type**: `minikube`

---

## Task 1: Deploy NGINX Web Server to K8s

**Objective**: Deploy the NGINX web server application onto the K8s cluster.

- Create Kubernetes Helm chart (start by using helm create) to deploy the NGINX web server application (Deployment manifest, Service manifest, etc..)
- Create a simple web page for the NGINX web server using a K8s `ConfigMap` object.
- Configure necessary environment variables, secrets, and volume mounts required by the NGINX server.

---

## Task 2: Deploy Microservice Caller

**Objective**: Deploy an additional microservice that will endlessly call the NGINX simple web page.

- Create Kubernetes a Helm chart to deploy the microservice caller application.
- It could be a simple script that uses the `curl` command.

---

## Task 3: Ingress Configuration

**Objective**: Set up an ingress controller to route incoming traffic to the NGINX web server.

- Deploy an Ingress Controller (like NGINX Ingress Controller).
- Define Ingress resources to expose the NGINX web server to external traffic.
- Configure Ingress rules for routing HTTP requests to the NGINX server's pods.

---

## Task 4: Bonus - Automatic Deployment via ArgoCD

**Objective**: Automate deployments with ArgoCD.

- Set up **ArgoCD** and **Gitea** using Helm packages on your local Minikube.
- Commit the YAMLs and code into the local Git server (Gitea).
- Configure ArgoCD (running on Minikube) to perform automatic deployments upon YAML/code changes.


<br>

---

## 🧪 2nd Exercise

- Check out some of the helm charts in our tactic environment.  
- Refer to the official [Helm documentation](https://helm.sh/docs/) to better understand chart structure and syntax.