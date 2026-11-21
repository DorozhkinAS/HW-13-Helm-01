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


[Chart.yaml](myapp/Chart.yaml)  
[volues.yaml](myapp/values.yaml)  
[frontend-deployment.yaml](myapp/templates/frontend-deployment.yaml)  
[backend-deployment.yaml](myapp/templates/backend-deployment.yaml)  


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


И проверяем теги образов:

```bash
kubectl get pod -n app1 -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
kubectl get pod -n app1 -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```
<img width="1073" height="163" alt="image" src="https://github.com/user-attachments/assets/1ad812dc-4a11-4d5f-853f-e2be4707da4f" />
