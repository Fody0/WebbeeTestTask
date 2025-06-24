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


mkdir ~/nginx && cd ~/nginx
```

Теперь находясь в папке nginx перемещаем туда файлы [docker-compose.yml](docker-compose.yml) и [nginx.conf](nginx.conf) и папку certs

Теперь нужно сгенерировать сертификаты

```
mkdir -p ~/nginx/certs

cd ~/nginx/certs

openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout cert.key -out cert.crt \
  -subj "/C=US/ST=State/L=City/O=Company/CN=vm1.local"
```

Для запуска контейнеров на второй машине уже должен работать докер, поэтому лучше перейти к гайду для второй машины и сначала полностью настроить ее - [гайд для второй машины](../vm2/ConfigurationGuide.md)

После этого можно поднимать контейнер и все должно работать

```
docker-compose build -d
docker-compose up -d
```

Далее переходим или к настройке второй машины или к концу основного гайда, [настройка VM2](../vm2/ConfigurationGuide.md), [основной гайд](../README.md)


### Доп задание

Для решения доп задания с масками нужно скачать dnsmasq

```
sudo apt install dnsmasq
```

После этого настроить его добавив в конец файла строку

address=/vm1.local/192.168.56.10

```
sudo nano /etc/dnsmasq.conf
```
После изменений сохраняем конфиг и перезапускаем сервис
```
sudo systemctl restart dnsmasq
```

Теперь c хоста можно обращаться к vm1 не через адрес 192.168.56.10, а через имя vm1.local
