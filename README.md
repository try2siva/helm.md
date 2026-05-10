# helm.md

# Helm Series

- Helm Playlist       https://youtube.com/playlist?list=PLo1yH22cI23KhA3wtf03DULo_oLrDzdra&si=9qmTvG9x2wEkr8Ab 
- Helm official page  https://helm.sh/  
- Helm cheat sheet    https://helm.sh/docs/intro/cheatsheet/ 

## Part 1 - Helm intro 

- why helm
- what is helm chart
- Advantages of helm chart
- Architecture of helm chart
- Use Cases

## Part 2 - Helm installation
- Provision AWS EC2 - Amazon Linux - t3.medium (chargeable) 
- Minikube Installation  https://github.com/iamarunix/kubernetes/blob/main/minikube%20installation 
- $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
- $ chmod 700 get_helm.sh
- $ ./get_helm.sh
- $ helm version

## Part 3 - Helm chart 

- helm list 
- helm create hotel 
- helm install hotel-hilton hotel
- helm install --ns hotel-hilton hotel
- helm install jen bitnami/jenkins 
- helm install webserver --debug --dry-run nginx
- helm pull bitnami/jenkins 
- helm rollback jen 43
- helm upgrade fox 
- helm lint jen
- helm uninstall fox 

## Part 4 - Helm repo 

- helm repo list 
- helm repo add bitnami https://charts.bitnami.com/bitnami
- helm search repo # to check all available charts 
- helm search repo mysql # to check particular chart 
- helm repo update
- helm repo remove bitnami

## Part 5 - Public helm chart 

- https://github.com/bitnami/charts/tree/main/bitnami 
- https://github.com/bitnami/charts/tree/main/bitnami/mongodb
- helm pull oci://registry-1.docker.io/bitnamicharts/mongodb
- helm install mongodb-1 mongodb
- helm upgrade mongodb-1 mongodb

## Part 6 - Create your own helm chart - Python App
- The flow ( script.py > dockerfile > docker image >  push image to docker hub > pull docker image into helm chart )
- python3 --version
- $ cat script.py
```
from datetime import datetime
dt = datetime.now()
print("Current Date and Time is:", dt )
print("Py app started ...")
print("Py app terminated")_
```
- Docker login 
- cat dockerfile
```
FROM python:3.8
WORKDIR /app
COPY . /app
CMD ["python3", "script.py"]
```
$ docker build -t python-app .
$ docker tag python-app arunsre/helm:t-python-app
$ docker push arunsre/helm:t-python-app	

- Verify the image in hub.docker.com
- Customize chart values to pull our image from hub.docker.com 
- Disable the appversion from Chart.yaml 
- Edit the values.yaml 
```
image:
  repository: arunsre/helm
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: "t-python-ck"
 
  #livenessProbe:
  #httpGet:
  # path: /
  #  port: http
  #readinessProbe:
  #httpGet:
  # path: /
  #  port: http
```
- helm install r-py-app py-app
- Verify k8s objects 
- kubectl get all 
- Verify output 
- kubectl logs <pod-name

## Part 7 - Create your own helm chart - Nginx App

- The flow ( dockerfile > docker image >  push image to docker hub > pull docker image into helm chart )
- Create index.html file 
```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Output of Nginx Webserver</title>
</head>
<body>
  <h2>Ready expose to your content to the world</h2>
</body>
</html>
```

$ cat dockerfile
```
FROM nginx
COPY index.html /usr/share/nginx/html
```
- Docker login 
- $ docker build -t nginx-app .
- $ docker tag python-app arunsre/helm:t-nginx-app
- $ docker push arunsre/helm:t-nginx-app	
- Verify the image in hub.docker.com
- helm create Nike-app
- Customize chart values to pull our image from hub.docker.com 
- Disable the appversion from Chart.yaml 
- Edit the values.yaml 
```
image:
  repository: arunsre/helm
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: "t-nginx-ck"
 
  #livenessProbe:
  #httpGet:
  # path: /
  #  port: http
  #readinessProbe:
  #httpGet:
  # path: /
  #  port: http

service:
  type: NodePort
  port: 80
```
- helm install nike-rev-1 nike-app 
- $ kubectl get all 
- $ kubectl logs <pod-name> 
- $ minikube service service-name --url
- $ curl url

## Part 8  Helm lint  

- helm lint <chart-name>
- helm lint helloworld
	space
	remove a variable 		

# part 9 helm value update 

helm upgrade r1 helloworld/ --set replicaCount=2

# part 10 helmfile  

- Install helmfile
- Download helmfile_linux_amd64 from https://github.com/roboll/helmfile/releases
- $ wget helmfile_linux_amd64
- $ mv helmfile_linux_amd64 helmfile             
- $ mv helmfile /usr/local/bin/
- $ chmod 777 /usr/local/bin/helmfile 
- $ helmfile -version
- $ helmfile-bank.yaml

```
repositories:
- name: prometheus
  url: https://prometheus-community.github.io/helm-charts
releases:
- name: helloworld
  chart: ./helloworld
  installed: true
```

$ helmfile --file helmfile-bank.yaml sync
$ helmfile --file helmfile-bank.yaml destroy

