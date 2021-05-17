> **Все работы проводим с хоста master-1**

### Подключаем PDB и проверяем его работу

1) Переходим в директорию с практикой
```bash
cd /srv/hl2021/pdb
```

2) Открываем `nginx/ingress.yaml` и в нем правим 
```yaml
    - host: nginx.s<номер своего логина>.edu.slurm.io
```

3) Запускаем тестовое приложение
```bash
kubectl apply -f nginx/
```

4) Имитируем деплой/проблему. Оставляем у приложения только одну реплику
```bash
kubectl scale deployment nginx --replicas 1
```

5) Смотрим на Pod'ы
```bash
kubectl get pod -o wide
```

Видим
```bash
NAME                    READY   STATUS    RESTARTS   AGE   IP           NODE                      NOMINATED NODE   READINESS GATES
nginx-7b94778dd-wlml5   1/1     Running   0          47s   10.244.3.5   node-1.sXXXXXX.slurm.io   <none>           <none>
```
> **Запоминаем на какой ноде запущен Pod**

6) В соседнем терминале запускаем команду для проверки доступности приложения

> Не забываем менять плэйсхолдеры

```bash
while true; do curl -s --connect-timeout 1 nginx.s<ваш номер логина>.edu.slurm.io -i | grep '200 OK' 2>&1 > /dev/null; if [ $? -eq 0 ]; then echo OK; else echo FAIL; fi; sleep 1; done
```

7) Выводим ноду на обслуживание
```bash
kubectl drain node-X.s<ваш номер логина>.slurm.io --grace-period 60 --delete-local-data --ignore-daemonsets --force
```
Видим что на какое-то время наше приложение становится недоступным

8) Возвращаем ноду
```bash
kubectl uncordon node-X.s<ваш номер логина>.slurm.io
```

9) Далее применяем pdb
```bash
kubectl apply -f pdb.yaml
```
И повторяем шаги с get pod

10) Смотрим на поды
```bash
kubectl get pod -o wide
```
Видим
```bash
NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE                      NOMINATED NODE   READINESS GATES
nginx-7b94778dd-4fp9m   1/1     Running   0          5m31s   10.244.4.3   node-2.sXXXXXX.slurm.io   <none>           <none>
```
> Запоминаем на какой ноде запущен pod

11) В соседнем терминале запускаем команду для проверки доступности приложения (если ранее ее останавливали)
```bash
while true; do curl -s --connect-timeout 1 nginx.s<ваш номер логина>.edu.slurm.io -i | grep '200 OK' 2>&1 > /dev/null; if [ $? -eq 0 ]; then echo OK; else echo FAIL; fi; sleep 1; done
```

12) Выводим ноду на обслуживание
```bash
kubectl drain node-X.s<ваш номер логина>.slurm.io --grace-period 60 --delete-local-data --ignore-daemonsets --force
```

13) Видим, что Kubernetes просит нас подождать
```bash
error when evicting pod "nginx-56f65c4885-kd97j" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
```
А наше приложение все еще доступно

14) Имитируем возвращение потерянного второго инстанса
```bash
kubectl scale deployment nginx --replicas 2
```

После этого процесс дрэйна у нас завершается успешно, а приложение работает без единой ошибки клиенту.

15) Возвращаем ноду
```bash
kubectl uncordon node-X.s<ваш номер логина>.slurm.io
```

16) Чистим за собой кластер
```bash
kubectl delete -f nginx/
kubectl delete -f pdb.yaml
```
