# Домашнее задание к занятию "`Docker. Часть 2`" - `Стрельников Александр Михайлович`

---

### Задание 1

Напишите ответ в свободной форме, не больше одного абзаца текста.

Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.

### Решение:

Docker Compose был установлен во время выполнения домашнего задания "'Docker. Часть 1'". 

Docker Compose используется для одновременного управления несколькими контейнерами, входящими в состав приложения, когда Docker без Compose применяется для управления контейнерами отдельно.
Docker Compose может улучшить мою жизнь благодоря тому, что мне не придется вручную настраивать каждый компонент (базу данных, веб-сервер, мониторинг и т.д.). Одним файлом docker-compose.yml и несколькими командами, я смогу описать и запустить целый стек приложений.

---

### Задание 2

Выполните действия и приложите текст конфига на этом этапе.

Создайте файл docker-compose.yml и внесите туда первичные настройки:

 * version;
 * services;
 * volumes;
 * networks.

При выполнении задания используйте подсеть 10.5.0.0/16.
Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw.
Все приложения из последующих заданий должны находиться в этой конфигурации.

### Решение:

Первичные настройки внесены, добавлена подсеть с именем strelnikovam-my-netology-hw. В моей версии Docker Compose необязательно указывать версию, поэтому в коде она не указана.

Содержимое файла docker-compose.yml

```
services:

volumes:

networks:
  strelnikovam-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 3 

**Выполните действия:** 

1. Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus. 
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории [6-04/prometheus](https://github.com/netology-code/sdvps-homeworks/tree/main/lecture_demos/6-04/prometheus) ).
3. Обеспечьте внешний доступ к порту 9090 c докер-сервера.

### Решение:

1. Конфигурация в docker-compose для Prometheus создана. Задано имя strelnikovam-netology-prometheus.
2. Конфигурация prometheus из репозитория скопирована. В docker-compose добавлен том.
3. Внешний доступ к порту 9090 с докер-сервера обеспечен.

```
services:
  prometheus:
    container_name: strelnikovam-netology-prometheus
    image: prom/prometheus:v2.36.2
    volumes:
      - ./prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    ports:
      - 9090:9090
    links:
    depends_on:
    networks:
      - strelnikovam-my-netology-hw
    restart: always

volumes:
  prometheus_data: {}

networks:
  strelnikovam-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 4 

**Выполните действия:**

1. Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway. 
2. Обеспечьте внешний доступ к порту 9091 c докер-сервера.

### Решение:

1. Конфигурация в docker-compose для Pushgateway создана. Задано имя strelnikovam-netology-pushgateway.
2. Внешний доступ к порту 9091 с докер-сервера обеспечен.

```
services:

  prometheus:
    container_name: strelnikovam-netology-prometheus
    image: prom/prometheus:v2.36.2
    volumes:
      - ./prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    ports:
      - 9090:9090
    links:
      - pushgateway:pushgateway
    depends_on:
      - pushgateway
    networks:
      - strelnikovam-my-netology-hw
    restart: always

  pushgateway:
    container_name: strelnikovam-netology-pushgateway
    image: prom/pushgateway
    restart: always
    expose:
      - 9091
    ports:
      - "9091:9091"
    networks:
      - strelnikovam-my-netology-hw

volumes:
  prometheus_data: {}

networks:
  strelnikovam-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 5 

**Выполните действия:** 

1. Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana. 
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории [6-04/grafana](https://github.com/netology-code/sdvps-homeworks/blob/main/lecture_demos/6-04/grafana/custom.ini).
3. Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
4. Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.

### Решение:

1. Конфигурация в docker-compose для Pushgateway создана. Задано имя strelnikovam-netology-grafana.
2. Конфигурация Grafana из репозитория скопирована. В docker-compose добавлен том.
3. Переменная окружения с путем до файла с кастомными настройками добавлена, в файле прописаны логин и пароль
4. Внешний доступ к порту 3000 с порта 80 докер-сервера обеспечен.

```
services:

  prometheus:
    container_name: strelnikovam-netology-prometheus
    image: prom/prometheus:v2.36.2
    volumes:
      - ./prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    ports:
      - 9090:9090
    links:
      - pushgateway:pushgateway
    depends_on:
      - pushgateway
    networks:
      - strelnikovam-my-netology-hw
    restart: always

  pushgateway:
    container_name: strelnikovam-netology-pushgateway
    image: prom/pushgateway
    restart: always
    expose:
      - 9091
    ports:
      - "9091:9091"
    networks:
      - strelnikovam-my-netology-hw

  grafana:
    container_name: strelnikovam-netology-grafana
    image: grafana/grafana
    user: "472"
    depends_on:
      - prometheus
    ports:
      - 80:3000
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning/:/etc/grafana/provisioning/
      - ./grafana/grafana.ini:/etc/grafana/grafana.ini
    env_file:
      - ./grafana/config.monitoring
    networks:
      - strelnikovam-my-netology-hw
    restart: always

volumes:
    prometheus_data: {}
    grafana_data: {}

networks:
  strelnikovam-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 6 

**Выполните действия.**

1. Настройте поочередность запуска контейнеров.
2. Настройте режимы перезапуска для контейнеров.
3. Настройте использование контейнерами одной сети.
4. Запустите сценарий в detached режиме.

### Решение:

1. Поочередность уже настроена в depends_on
2. Режим перезапуска уже настроен в restart - значение always
3. Сеть уже настроена в networks
4. Сценарий запущен в detached режиме с помощью команды docker compose up -d


---

### Задание !

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
