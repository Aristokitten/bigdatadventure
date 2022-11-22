# Big Datadventure
Well, yes... this was a huge adventure. Once, in the plethora of tutorials and blogs I had read, I came across this [Infographic](https://toggl.com/blog/seven-levels-developer-hell) with the seven circles of developer hell and let me tell you - I can relate. Although there were no clients, I felt like there were. All in all, I learned a lot and - although it is a small step for probably everyone else here on GitHub - it was a huge step for me. 
This project was delivered as a result of the course "Big Data Programming", taught by Bernhard Ortner at the DHBW Stuttgart. Goal of this ReadMe document is to give a brief overview of the most important information relating to solving **Task 2: Helm und Kubernetes Microservice**. (Yes, there is also a Task 1: On Premise Big Data Engines, please refer to this [repository](https://git.dhbw-stuttgart.de/wi21010/bigdata_wi21010vs_wi21015ll_s3) on GitLab)

## Task fulfillment
### Subtask 1: Basics - Installation of Helm and Minikube
- Installation of **helm**, an open source tool, which simplifies working with Kubernetes (or in my case: minikube) and eases the deployment of kubernetes applications, and also includes other features such as a version control system. Here I followed this [helm documentation](https://helm.sh/docs/intro/install/) and installed helm via homebrew for MacOs with `brew install helm`
- In addition, with `brew install minikube` and this [minikube website](https://minikube.sigs.k8s.io/docs/start/), I installed **minikube**. Minikube is a local and lightweight Kubernetes implementation and makes it easy (well.. as "easy" as is) to learn and develop for Kubernetes - a famous container management; it creates a VM on a local PC (in this case, my Mac) and deploys a simple cluster that contains only one node. For this to work, Docker container (or a Virtual Machine environment) is needed, too. 
- based on the command `minikube version` and `helm version`, I tested whether the installation was successful; result: helm and minikube are available and usable! [Yay](task2a_success.png)

### Subtask 2: Level Newbie - Developing a Docker Application 
Goal of this subtask was to dockerize a microservice: 
- First, I developed a **python app** (app.py) that includes a flask microservice 
- Second, I created a resource file that specified the **requirements** (in the requirements.txt), namely ``Flask == 2.0.0`` 
- Third, I wrote the **Dockerfile** that e.g.: 
  - intitializes the base image with the latest python and reduced its size (FROM)
  - avoids dependency issues (RUN)
  - installs the dependencies that are specified in said requirements.txt file (RUN) 
  - exposes a port so that it can be accessed from outside (EXPOSE) 
  - starts the container (CMD)
- Lastly, on the path where app.py is saved, I built and ran the docker container with the following commands:
  - `docker image build -t aufgabe2bfinal`
  - optional a check in between: `docker image ls`
  - `docker run -p 80:80 -d aufgabe2bfinal`

Result: my flask application is installed in a container and runs in it accordingly! To go sure, I tested this via **curl**: `curl http://127.0.0.1:80`. [See, it worked](task2b_curl_success.png)! Further, in docker desktop, I can see the log files since creation. 
<br>
Problems encountered (and solved):
- finding and specifying the right Flask version 
- getting an overview of the files that are required to build image and container 
- writing Dockerfile instead of dockerfile... WHY THOUGH?! 
- finding out that my container had a size of nearly 1000 MB. Understanding why; googling; realizing that my python base image is the guilty one. Reduced it with a minimized docker image, namely `python:3.10-slim-bullseye`

### Subtask 3: Level Hackerman - Installing the Docker Container in Minikube via Helm
After creating the components, the next objective was to transform the microservice into a helm chart.

1. First, I created the **image** in minikube's dockerservice: 
```
# 1. start and access virtual machine 
minikube start    
# to specify the VM, type: minikube start --driver=<virtualbox/hyperkit/....>
eval $(minikube -p minikube docker-env)

# 2. optional: validate redirection to minikube's docker servier
minikube status  

# 3. build image that I called "aufgabe2cimage"; don't forget the space and dot at the end of the command! (happened to me more often than I can admit)
docker build -t aufgabe2cimage . 
# this is optional and given as default anyways: docker build -t <imageName> -f./Dockerfile .

# 4. optional: check the image just built
docker images
```

2. Second, I created the **helmchart** that I called "vronichart": 
```
# 1. create chart
helm create vronichart

# 2. optional: validate that the chart was created
helm list 
```

3. Third, I cleaned the **chartfiles**: deleted the redundancies, unncessary files and adapted the settings: 
- deleted: serviceaccounts.yaml, notes.txt, commented out ingress.yaml (as this is for production environment and not deployment)
- files I kept: hpa.yaml for future scaling, Chart.yaml for releasing updates, deployment.yaml for specifying the configuration for a deployment object
- changed deployment.yaml: 
  - ports: referenced to *.Values.service.port* for container port
  - service type: *NodePort* instead of ClusterIP with port *80* and nodePort specified to *28600*
- changed value.yaml:
  - image: for *repository* I referenced to my Git Container Registry repository with *ghrc.io/aristokitten/aufgabe2cimage* and adapted the *pullPolicy* to *IfNotPresent*
<br>

4. Fourth, I **installed the chart** called "aufgabe2cimage" in a namespace that I called "myspace" in a folder called "vronichart"; without this, the chart would just appear in "default". 
```
helm install aufgabe2cimage --namespace myspace --create-namespace ./vronichart
```

5. Fifth, I **packaged** the chart with the name "vronichart", resulting in a nicely pacakged .tgz file: 
```
helm package vronichart --debug
```

Lastly, I ran several **tests**. For example: `kubectl get svc -name myspace` which shows running services, as well as: `helm install --dry-run vronichart-0.1.0.tgz --generate-name` to preview what would be sent to the cluster, without really submitting it. <br>

Ultimately, I **made sure everything worked** by following these steps: 
- closing everything, rebooting my PC
- `minikube start` in one terminal window
- `minikube dashboard`in a separate terminal window; dashboard shows my running microservice 
- `minikube service list`shows my services
- `minikube service aufgabe2cimage-vronichart -n myspace` returns URL
- when following the URL, my **microservice successfully** opened

## Further Problems and Learnings 
- there was always something I could do better: a mistake I found in hindsight, a new idea that came to my mind etc. Hence, I learned to **update** my chart like this:
```
# upgrading the chart (of 0.1.0.tgz)
helm upgrade aufgabe2cimage -n myspace ./vronichart-0.1.0.tgz

# packaging the new chart (which will then package it into 0.2.0.tgz)
helm package vronichart --debug
``` 
This is the reason why - in the meantime - I have deployed several versions of my microservice. 

- **testing** to open my microservice from another local machine made me realize that I still had to take care of a lot of things, since the image couldn't be openend from another person without actually performing all the steps (build image, insert image name etc. etc...). Solution: either provide clear installation manual, or work with **Github Container Registry**. This is the way, I told myself. 
- Hence, I registered for Github Container Registry and, ultimately, succeeded in uploading my **image under packages** [here](https://github.com/Aristokitten?tab=packages), which is also part / linked in my [bigdatadventure repository](https://github.com/Aristokitten/bigdatadventure). 
- In this way, it is possible to **install my image with just one command**: `docker pull ghcr.io/aristokitten/aufgabe2cimage:latest`  

