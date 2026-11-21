# Домашнее задание к занятию «Helm» - `Дорожкин Артем`


```bash
# установка helm
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version
echo 'source <(helm completion bash)' >> ~/.bashrc

# создание нового chart
helm create myapp 

```

Chart.yaml

```
apiVersion: v2
name: myapp
description: A Helm chart for Kubernetes

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 0.1.0

# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
appVersion: "1.0.0"
```

volues.yaml

```
frontend:
  replicaCount: 1
  image:
    repository: nginx
    tag: "1.25"
    pullPolicy: IfNotPresent

backend:
  replicaCount: 1
  image:
    repository: httpd
    tag: "2.4"
    pullPolicy: IfNotPresent
```

frontend-deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}-frontend
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
    app.kubernetes.io/component: frontend
spec:
  replicas: {{ .Values.frontend.replicaCount }}
  selector:
    matchLabels:
      {{- include "myapp.selectorLabels" . | nindent 6 }}
      app.kubernetes.io/component: frontend
  template:
    metadata:
      labels:
        {{- include "myapp.selectorLabels" . | nindent 8 }}
        app.kubernetes.io/component: frontend
    spec:
      containers:
        - name: frontend
          image: "{{ .Values.frontend.image.repository }}:{{ .Values.frontend.image.tag }}"
          imagePullPolicy: {{ .Values.frontend.image.pullPolicy }}
          ports:
            - containerPort: 80
              name: http
```

backend-deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}-backend
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
    app.kubernetes.io/component: backend
spec:
  replicas: {{ .Values.backend.replicaCount }}
  selector:
    matchLabels:
      {{- include "myapp.selectorLabels" . | nindent 6 }}
      app.kubernetes.io/component: backend
  template:
    metadata:
      labels:
        {{- include "myapp.selectorLabels" . | nindent 8 }}
        app.kubernetes.io/component: backend
    spec:
      containers:
        - name: backend
          image: "{{ .Values.backend.image.repository }}:{{ .Values.backend.image.tag }}"
          imagePullPolicy: {{ .Values.backend.image.pullPolicy }}
          ports:
            - containerPort: 80
              name: http
```

```bash
# создание своих namespace
kubectl create namespace app1 
kubectl create namespace app2
# или
kubectl create ns app1
kubectl create ns app2
```

<img width="327" height="88" alt="image" src="https://github.com/user-attachments/assets/73b89ce9-5f94-434a-9e6a-d2930e81c94e" />

## Запускаем релизы:

```bash
helm install myapp-v1 ./myapp -n app1
helm install myapp-v2 ./myapp -n app1 --set frontend.image.tag=1.24
helm install myapp-v3 ./myapp -n app2 --set frontend.image.tag=1.26 --set backend.image.tag=2.4.58
```

<img width="859" height="397" alt="image" src="https://github.com/user-attachments/assets/3c6c1add-582c-4a69-91ae-992e9152f357" />

## Результат:

<img width="1039" height="305" alt="image" src="https://github.com/user-attachments/assets/8dd000af-df8c-4aeb-afbe-066fcaf30f6a" />

И проверяем теги образов:

```bash
kubectl get pod -n app1 -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
kubectl get pod -n app1 -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```
<img width="1073" height="163" alt="image" src="https://github.com/user-attachments/assets/1ad812dc-4a11-4d5f-853f-e2be4707da4f" />
