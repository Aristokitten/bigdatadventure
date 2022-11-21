# Big Datadventure
Well, yes... this was a huge adventure. Once, in the plethora of tutorials and blogs I had read, I came across this [Infographic](https://toggl.com/blog/seven-levels-developer-hell) with the seven circles of developer hell and let me tell you - I can relate. Although there were no clients, I felt like there were. All in all, I learned a lot and - although it is a small step for probably everyone else here on GitHub - it was a huge step for me. 
This project was delivered as a result of the course "Big Data Programming", taught by Bernhard Ortner at the DHBW Stuttgart. Goal of this ReadMe document is to give a brief overview of the most important information relating to solving **Task 2: Helm und Kubernetes Microservice**. (Yes, there is also a Task 1: On Premise Big Data Engines, please refer to this [repository](https://git.dhbw-stuttgart.de/wi21010/bigdata_wi21010vs_wi21015ll_s3) on GitLab)

## Task fulfillment
### Subtask 1: Basics - Installation of Helm and Minikube
- Installation of **helm**, an open source tool, which simplifies working with Kubernetes (or in my case: minikube) and eases the deployment of kubernetes applications, and also includes other features such as a version control system. Here I followed this [helm documentation](https://helm.sh/docs/intro/install/) and installed helm via homebrew for MacOs with `brew install helm`
- In addition, with `brew install minikube` and this [minikube website](https://minikube.sigs.k8s.io/docs/start/), I installed **minikube**. Minikube is a local and lightweight Kubernetes implementation and makes it easy (well.. as "easy" as is) to learn and develop for Kubernetes - a famous container management; it creates a VM on a local PC (in this case, my Mac) and deploys a simple cluster that contains only one node. For this to work, Docker container (or a Virtual Machine environment) is needed, too. 
- based on the command `minikube version`and `helm version`, I tested whether the installation was successful; result: helm and minikube are available and usable

### Subtask 2: Level Newbie - Developing a Docker Application 
- python app schreiben
- requirements.txt schreiben
- Dockerfile schreiben und auf requirements verweisen

- docker image build -name-
- docker run

### Subtask 3: Level Hackerman - Installing the Docker Container in Minikube via Helm


## Commands 

## Problems and Learnings 
