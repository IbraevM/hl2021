> **Все работы все еще проводим с хоста master-1**

### Создаем новый Priority Class в Kubernetes

1) Переходим в директорию с практикой
```bash
cd /srv/hl2021/priorityclasses
```

2) Смотрим PC в кластере
```bash
kubectl get pc
```

3) Создаем Priority Class'ы и Namespace'ы для наших приложений:
```bash
kubectl create -f pc-prod.yaml
kubectl create -f pc-develop.yaml

kubectl create ns production
kubectl create ns development
```

4) Деплоим наши приложения
```bash
kubectl create -f deployment-prod.yaml -n production
kubectl create -f deployment-develop.yaml -n development
```

5) Смотрим, что всё задеплоилось. Смотрим также ресурсы нод

```bash
kubectl get pod -n production
kubectl get pod -n development

kubectl describe node node-1.s<ваш номер логина>.slurm.io
kubectl describe node node-2.s<ваш номер логина>.slurm.io
```

> Видим что количество доступных ресурсов осталось небольшим.

6) Эвакуируем pod'ы с ноды. Смотрим на Pod'ы production и development окружения. Наш Production должен работать

```bash
kubectl drain node-2.s<ваш номер логина>.slurm.io --ignore-daemonsets

kubectl get pod -n production
kubectl get pod -n development
```

7) Подчищаем за собой:

```bash
kubectl delete deployment --all -n production
kubectl delete deployment --all -n development

kubectl uncordon node-2.s<ваш номер логина>.slurm.io
```

8) Правим манифест Deployment для Production окружения так, чтобы у него было `Requests: 800 mCPU`

9) Снова деплоим наши приложения:

```bash
kubectl create -f deployment-prod.yaml -n production
kubectl create -f deployment-develop.yaml -n development
```

10) Смотрим, а что у нас там с Flannel? :)
