# Инструкция к применению

1) Заходим на jump-хост:

```bash
ssh s<ваш номер логина>@sbox.slurm.io
```

2) Переходим с jump-хоста на Master-ноду вашего кластера:

```bash
ssh master-1.s<ваш номер логина>
```

3) Становимся рутом!

```bash
sudo -s
```

4) Клонируем репозиторий на сервер:

```bash
cd /srv
git clone https://github.com/IbraevM/hl2021.git
```

5) Опционально: ставим k8sh:

```bash
yum install k8sh -y -q
```
