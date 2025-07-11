## Гайд для VM1


Запускаем машину, установится Ubuntu, заходим в свой аккаунт - который указывали при добавлении машины в VirtualBox.

Для начала убираем файл 50-cloud-init.yaml, чтобы сетевые настройки не сбрасывали каждый раз при перезапуске машины
```
sudo rm /etc/netplan/50-cloud-init.yaml
```
После этого заходим в файл 99-disable-network-config.cfg и добавляем там новую строку network: {config: disabled}, после чего сохраняем
```
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```
Теперь перемещаем в путь /etc/netplan/ файл [01-static-config.yaml](01-static-config.yaml) и пишем команду в терминале

```
sudo netplan apply
```

Сеть настроена, дальше нужно установить зависимости
```
sudo apt update
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker $USER
newgrp docker


mkdir ~/postgres && cd ~/postgres
```

Теперь находясь в папке postgres перемещаем туда файл [docker-compose.yml](docker-compose.yml) и папку certs

Теперь нужно сгенерировать сертификаты

```
mkdir -p ~/postgres/certs

cd ~/postgres/certs

openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout cert.key -out cert.crt \
  -subj "/C=US/ST=State/L=City/O=Company/CN=vm1.local"
```

После этого можно поднимать контейнер и все должно работать

```
docker-compose build -d
docker-compose up -d
```

Далее переходим или к настройке первой машины или к концу основного гайда, [настройка VM1](../vm1/ConfigurationGuide.md), [основной гайд](../README.md)
