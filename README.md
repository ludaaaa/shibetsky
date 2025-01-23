# Установить Linux Oracle на VirtualBox:


Установить образ Linux

Выделить 4 ядра

Выделить 4096 МБ оперативной памяти




# Установка docker

Устанавливает утилиту wget на систему

     sudo yum install wget

![image](https://github.com/user-attachments/assets/bc4a71b3-cb7f-4867-9e92-e06857dab509)


Скачиваем файл репозитория

     sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo

![image](https://github.com/user-attachments/assets/605d0a17-dc18-46a6-bd93-8f2b74763150)


Устанавливаем docker

     sudo yum install docker-ce docker-ce-cli containerd.io

![image](https://github.com/user-attachments/assets/b20d38b6-0dfa-4a02-95cc-e73d46e54f39)


Запускаем его и разрешаем автозапуск

     sudo systemctl enable docker --now

![image](https://github.com/user-attachments/assets/8d462c47-b7b5-484e-9235-b7ceb4e1ff6f)


# Установка compose

Для начала нужно убедиться в наличии пакета curl

     sudo yum install curl

![image](https://github.com/user-attachments/assets/dfac57ab-3f82-4d71-8c6e-4aa97a0392c1)



Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

     COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

     sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose

![image](https://github.com/user-attachments/assets/bc557101-0a16-4532-b0ac-ca95899c02ff)


Предоставление прав на выполнение файла docker-compose

     sudo chmod +x /usr/bin/docker-compose

Проверка установленной версии Docker Compose

     sudo docker-compose --version
     
![image](https://github.com/user-attachments/assets/2ccb55aa-4bac-4883-82a2-cae401576154)


# Делаем grafana

Установка git

     sudo yum install git

![image](https://github.com/user-attachments/assets/84868448-695d-4d2e-a218-515e339300fd)


Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

     sudo git clone https://github.com/skl256/grafana_stack_for_docker.git

![image](https://github.com/user-attachments/assets/cf396333-1878-4e77-a45f-06e58fb58fbe)


Заходит в папку - cd

     cd grafana_stack_for_docker

cd .. - возвращает в папку выше

![image](https://github.com/user-attachments/assets/980a64c8-af4c-4f7e-bb3e-f4156194d5cc)


(После этого можно вставлять готовый docker-compose)

Cоздаем папки двумя разными способами

     sudo mkdir -p /mnt/common_volume/swarm/grafana/config

     sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}

Выдаем права

     sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}

Создаем файл

     sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini

Копирование файлов

     sudo cp config/* /mnt/common_volume/swarm/grafana/config/

Переименовывание файла

     sudo mv grafana.yaml docker-compose.yaml

![image](https://github.com/user-attachments/assets/9e15984d-cc9b-47a0-94d1-3b5f47219b66)


Собрать докер (нужно запускать из папки где docker-compose.yaml)

     sudo docker compose up -d

Опустить докер - sudo docker compose stop

![image](https://github.com/user-attachments/assets/3fb63112-de4f-46eb-8ee7-247d094426f4)



# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi docker-compose.yaml

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/f59c5315-f131-459e-b705-9838f9a880a4)

выйти не сохраняясь из vim - esc -> :q!

выйти сохраняясь из vim - esc -> :wq!

Заходим в другую папку 

      cd /mnt/common_volume/swarm/grafana/config

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi prometheus.yaml
     
![image](https://github.com/user-attachments/assets/2fffe888-860a-4d06-af6d-a08eefc78482)


Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/0cc96cc2-e9bb-4786-877d-9b8ca5752a53)



# Делаем grafana на сайте

   •	переходим на сайт localhost:3000

                User & Password GRAFANA: admin

                Код графаны: 3000

                Код прометеуса: http://prometheus:9090

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку +Add visualization, а после "Configure a new data source"

                выбираем Prometheus

                Connection

                http://prometheus:9090

   •	Authentication

                Basic authentication

                User: admin

                Password: admin

                Нажимаем на Save & test и должно показывать зелёную галочку

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку "Import dashboard"

                Find and import dashboards for common applications at grafana.com/dashboards: 1860 //ждем кнопку Load

                Select Prometheus ждем кнопку "Import"


![image](https://github.com/user-attachments/assets/6ea1e5a4-a987-4d40-8a67-ded375ea0794)


![image](https://github.com/user-attachments/assets/38a0794a-4c25-4d36-87ea-507c92823059)




# Делаем VictoriaMetrics

Для начала зайдем в нужную папку

     cd grafana_stack_for_docker

Открываем docker-compose

     sudo vi docker-compose.yaml

После prometheus вставляем vmagent (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/d9180a78-3e2b-442e-8193-40b846a9a17c)



Захом в connection там где мы писали http://prometheus:9090 пишем http://victoriametrics:8428 И заменяем имя из "Prometheus-2" в "Vika" нажимаем на dashboards add visualition выбираем "Vika" снизу меняем на "code" Переходим в терминал и пишем

     echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus

• команда отправляет бинарные данные (например, метрики в формате Prometheus) на локальный сервер, который слушает на порту 8428.

     curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'

• команда делает запрос к API для получения данных по метрике OILCOINT_metric1

• команда выводит текст, который может быть использован для определения метрики в формате, совместимом с Prometheus

• команда выводит информацию о типе и значении этой метрики в формате, который может быть использован системой мониторинга Prometheus.

![image](https://github.com/user-attachments/assets/8d8fbb65-b658-4529-b779-49916968cf0b)


Значение 0 меняем на любое другое

Заходим на сайт localhost:8428 и открываем vmui

Копируем переменную OILCOINT_metric1 и вставляем в query

![image](https://github.com/user-attachments/assets/aca80109-d856-4a6c-9106-83045a3475c6)


Нажимаем run

Копируем переменную OILCOINT_metric1 и вставляем в code

![image](https://github.com/user-attachments/assets/c1c6f4fc-d8ca-45b6-a4ec-2b11d4dab338)

