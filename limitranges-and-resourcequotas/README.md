> **Все работы все еще проводим с хоста master-1**

### Создаем LimitRange и ResourceQuota

1) Переходим в директорию с практикой
```bash
cd /srv/hl2021/limitranges-and-resourcequotas
```

2) Создаем Namespace
```bash
kubectl create ns test
```

3) Создаем в нем Limitrange и Resourcequota
```bash
kubectl apply -f limitrange.yaml -f resourcequota.yaml -n test
```

4) Смотрим что получилось
```bash
kubectl describe ns test
```

5) Запускаем Deployment

```bash
kubectl apply -f deployment.yaml -n test
```

И еще раз заглядываем в описание ns.

> Видим что количество доступных ресурсов начало уменьшаться.

6) Подправим Deployment таким образом, чтобы в Limits теперь у него был 1 cpu

7) Смотрим describe последнего Replicaset. Возвращаем Limits как было

8) Проверяем на ошибку при скейлинге. Скейлим наше приложение до 100 реплик

9) Убеждаемся, что скейлить выше указанного порога нам не дают

10) Чистим за собой кластер
```bash
kubectl delete ns test
```
