# home-assistant

## Зміст
- [Інструкції, які використовуються для налаштувань](#1-settings-instructions)
- [Загальні нюанси](#2-general-nuances)
- [Configuration](#3-configuration)
  - [Portainer](#301-portainer)
  - [Home Assistant](#302-home-assistant)
  - [MariaDB](#303-mariadb)
  - [DBeaver](#304-dbeaver)
  - [Code Server](#305-code-server)
  - [Mosquitto](#306-mosquitto)
- [Офіційні інтеграції](#4-integrations)
- [HACS](#5-hacs)
  - [Налаштування HACS](#5001-setup-hacs)
  - [Tuya](#5002-tuya)
  - [Local Tuya](#5003-local-tuya)
  - [Sonoff](#5004-sonoff)
  - [Solarman](#5005-solarman)
- [Setup steps](#6-setup-steps)
- [Add menu item](#7-add-menu-item)

---

### 1. Settings instructions
Інструкції, які використовуються для налаштувань:
- Docker
  - [Встановлення Debian](https://io-home.ru/home-assistant/docker-container/docker-chast-1-ustanovka-debian/)
  - [Docker та Portainer](https://io-home.ru/home-assistant/docker-container/docker-chast-2-docker-i-portainer/)
  - [Home Assistant](https://io-home.ru/home-assistant/docker-container/docker-chast-3-home-assistant-container/)
  - [File Editor](https://io-home.ru/home-assistant/docker-container/docker-chast-4-file-editor/)
  - [MariaDB](https://io-home.ru/home-assistant/docker-container/docker-chast-5-mariadb/)
  - [Mosquitto MQTT](https://io-home.ru/home-assistant/docker-container/docker-chast-6-mosquitto-mqtt/)
  - [Zigbee2MQTT](https://io-home.ru/home-assistant/docker-container/docker-chast-7-zigbee2mqtt/)
  - [ESPHome](https://io-home.ru/home-assistant/docker-container/docker-chast-8-esphome/)
  - [Node-Red](https://io-home.ru/home-assistant/docker-container/docker-chast-9-node-red/)
  - [Bluetooth](https://io-home.ru/home-assistant/docker-container/docker-chast-10-bluetooth/)

---


### 2. General nuances
Загальні нюанси:
###### 1. Налаштування приватності проекта щоб був недоступний іншим юзерам
- https://github.com/seregaNV/home-assistant/settings

###### 2. Щоб можна було пушати - створюємо Access tocken
- https://github.com/settings/tokens/new

З вашого облікового запису GitHub перейдіть до `Settings` → `Developer Settings` → `Personal Access Token` → `Tokens (classic)` → `Generate New Token (Give your password)` → Заповніть форму → натисніть Згенерувати токен → буде виглядати приблизно так: `xxx_xxxxxxxxxxx`
  - new_token
  - 90 days
  - repo

###### 3. Фіксуємо в роутері статичний IP, щоб девайс не відвалювався.
- http://tplinkwifi.net/
- сетапимо статичний IP:
  - `'Додаткові налаштування' -> 'Мережа' -> 'DHCP-сервер' -> 'Резервування адрес' -> Додати`
- переіменовуємо девайс, наприклад в `sonoff_s60_tpf_001`, щоб простіше було шукати:
  - `'Схема мережі' -> 'Клієнти' -> обираємо необхідний девайс -> переіменовуємо`

###### 4. Проблема з актуалізацією інформації TOMZN TOMPD-63WIFI
Довго не міг пойняти чому відвалилась моніторилка в TOMZN TOMPD-63WIFI.\
'Енергія' оновлювалась приблизно 1 раз на хвилину, а от 'Живлення' та 'Струм' не оновлювалось взагалі.\
Помітив взаємозв'язок з актуалізацією даних в HA. Як тільки відкриваю мобільний додаток Tuya - дані про 'Живлення' та 'Струм' в HA починають оновлюватись майже кожну секунду. Як тільки додаток закриваю - оновлення даних зупиняються.\
Як вирішити проблеему з оновленням даних?
Не вдалось вирішити цю проблему, пробував
- офіційну інтеграцію `Tuya` - не оновлюється
- `HACS` офіційна інтеграцію `Tuya Local` - складно підключити та налаштувати (Tuya IoT Platform), не підтягує нормально девайси. **Спробувати ще раз**.
- `HACS` кастомна інтеграцію `Local Tuya` - не оновлюється
- `polling через TinyTuya + MQTT` - непонятні значення

---

### 3. Configuration

#### 3.01. Portainer
Portainer — зручний інтерфейс керування контейнерами (Docker/Kubernetes) з браузера.

1. Запускаємо
   - command: `docker compose up portainer`
2. Відкриваємо в браузері та налаштовуємо доступ
   - host: http://127.0.0.1:9002
   - configs:
     - username: `admin`
     - password: `0123456789pP`
3. Перевіряємо чи працює, чи підтягнулись контейнери

- нюанси налаштувань:
  - `environment: CSP=false` - не секюрно, може бути доступ за межами мережі

---

#### 3.02. Home Assistant
Домашній помічник – окрема установка основного домашнього помічника.

1. Запускаємо
    - command: `docker compose up home-assistant`
2. Відкриваємо в браузері та вносимо перші налаштування:
    - host: http://127.0.0.1:8123
    - 'Створи мій розумний дім'
    - 'Створити користувача' - вводимо дані:
      - name: `B14`
      - username: `serega_b14`
      - password: `3535353535hHaA`
    - 'Місцезнаходження будинку'
    - 'Допоможіть нам допомогти вам'
    - 'Все готово!'
    - Вмикаємо 'Розширений режим'
      `'B14' -> 'Налаштування користувача' -> 'Розширений режим'`
      Home Assistant за замовчуванням приховує розширені функції та параметри.
      Ви можете зробити ці функції доступними, перемкнувши цей перемикач.
      Це налаштування орієнтоване на поточного користувача, і не впливає на інших користувачів,
      які використовують Home Assistant.
3. Додаємо пункт [меню](#701-portainer) для `Portainer`

##### нюанси налаштувань:
- `volumes: /run/dbus:/run/dbus:ro` - Прокидування /run/dbus у Docker-контейнер потрібне,
  щоб контейнер міг підключатися до системної D-Bus шини хоста
  і взаємодіяти з системними сервісами `systemd`, `NetworkManager`, `BlueZ (Bluetooth)`,
  `logind`, `Avahi`, `UPower`, ...

---

#### 3.03. MariaDB
Локальна БД.

1. Запускаємо
    - command: `docker compose up mariadb`
2. Підключаємо MariaDB до Home Assistant
   - ініціалізуємо конфіг для підключення до `mariadb`
     додаємо конфіг в `data/home-assistant/config/secrets.yaml`
     ```YAML
       mariadb: "mysql://ha_user:OtPgHWk8zKDoY56F@mariadb/ha_db?charset=utf8mb4"
     ```
   - налаштовуємо підключення до `mariadb`
     додаємо конфіг в `data/home-assistant/config/configuration.yaml`
     ```YAML
       recorder:
         db_url: !secret mariadb
         purge_keep_days: 30
     ```

##### нюанси налаштувань:
- в самій IDE не відображаються дані з `/home-assistant/data/mariadb/data/ha_db`
  через права доступа, це не критично, якщо потрібно - дивимось в самому контейнері `mariadb` або в `mc`

---

#### 3.04. DBeaver
Віртуальна web-версія DBeaver для роботи з локальною БД

1. Запускаємо
    - command: `docker compose up cloudbeaver`
2. Відкриваємо в браузері та налаштовуємо доступ
    - host: http://127.0.0.1:8978
      - При першому запуску налаштовуємо користувача та логінимось:
        - Server Name *: HA
        - Allowed Server URLs: mariadb
        - Login *: db_user
        - Password *: VFYq7CFbkeam3vw
        - Repeat Password *: VFYq7CFbkeam3vw
      - New Connection:
        - Driver: MariaDB
        - Host *: mariadb
        - Port: 3306
        - User name: ha_user
        - User password: OtPgHWk8zKDoY56F
3. Перевіряємо чи працює, виконуємо декілька запитів
4. Додаємо пункт [меню](#702-dbeaver)

---

#### 3.05. Code Server
Віртуальна IDE, для можливості правити код на віртуальній машині

1. Запускаємо
    - command: `docker compose up code-server`
2. Відкриваємо в браузері
    - host: http://127.0.0.1:8443
3. Додаємо пункт [меню](#703-configurator)

---

#### 3.06. Mosquitto
Mosquitto — брокер повідомлень MQTT. Щоб дозволити пристроям використовувати модель публікації/підписки на обмін повідомленнями.\

1. Перед запуском потрібно створити пусті файліки, бо буде сипати помилки `Error: Unable to open config file '/mosquitto/config/mosquitto.conf'`:
   - `mkdir ./data/mosquitto`
   - `mkdir ./data/mosquitto/config`
   - `mkdir ./data/mosquitto/log`
   - `touch nano ./data/mosquitto/config/mosquitto.conf`
   - `touch nano ./data/mosquitto/log/mosquitto.log`
2. Запускаємо
   - command: `docker compose up mosquitto`
3. Після запуску командою додаємо юзера з паролем
   - `docker exec -it mosquitto mosquitto_passwd -c /mosquitto/config/mqttuser mqtt_user`
      - user: mqtt_user
      - pass: MumsaXHEBA7TAsLZ
4. Додаємо конфіги для `mosquitto`
    - створюємо файл конфігурації `./data/mosquitto/config/mosquitto.conf`
    - додаємо наступний код:
      ```nginx configuration
        persistence true
        persistence_location /mosquitto/data/

        listener 1883

        allow_anonymous false

        log_dest file /mosquitto/log/mosquitto.log
        log_dest stdout

        password_file /mosquitto/config/mqttuser
      ```
5. Перезапускаємо `mosquitto`
6. Додаємо інтеграцію
   - `Налаштування -> Пристрої та сервіси (Інтеграції) -> Додати інтеграцію -> MQTT`
    - `Брокер`: mosquitto
    - `Порт`: 1883
    - `Ім'я користувача`: mqtt_user
    - `Пароль`: MumsaXHEBA7TAsLZ
7. Підтверджуємо

- нюанси налаштувань:
    - `environment: CSP=false` - не секюрно, може бути доступ за межами мережі


---

### 4. Integrations
Офіційні інтеграції Home Assistant./

---

### 5. HACS
HACS (Home Assistant Community Store) – це магазин співтовариства Home Assistant.\
В цьому магазині викладено різні додатки, що певним чином покращують функціонал Home Assistant.

#### 5.001. Setup HACS
Встановлюємо та налаштовуємо `HACS`.\
Основним плюсом HACS є кастомізовані інтеграції, в яких набагато більше можливостей ніж в офіційних. Встановлення інтеграцій можна як через пошук, так і офіційно не додані в список `HACS` (через пряме посилання на GIT репозиторій).\
За основу хотів взяти відео [youtube](https://www.youtube.com/watch?v=DvKf1nPx5c0), але в ньому налаштування для `OS/Supervised`.\
Тому налаштування через команди в консолі.

##### Етапи встановлення:
1. Читаємо усі запущені контейнери: `docker ps -a`
2. Запускаємо bash в середині контейнера home-assistant: `docker exec -it home-assistant bash`
3. Виконуємо команду, можливо доведеться двічі щоб встановився додаток: `wget -O - https://get.hacs.xyz | bash -`
4. Виходимо та закриваємо термінал, перезапускаємо HA
5. Додаємо інтеграцію `HACS`
    - `Налаштування -> Пристрої та сервіси (Інтеграції) -> Додати інтеграцію -> HACS`
    - відмічаємо усі пункти
    - тиснемо 'Надіслати'
6. Нам пропонують авторизуватись в Git репозиторію - логінемось за інструкцією
    - https://github.com/login/device
    - 59A1-12A4
7. Інтеграція сама створює пункт меню, в ньому можемо користуватись `HACS`

---

#### 5.002. Tuya
Встановлюємо та налаштовуємо `Tuya` для підключення пристроїв через `ZigBee` шлюз.\

В `HA` є штатна інтеграція `Tuya`, через яку будуть підтягуватись усі пристрої.\
Але в неї є свої недоліки:
- доступні базові функції керування
- доступ лише через хмарний сервер (інтернет, WiFi)

В `HACS` також є інтеграція `Local Tuya`.\
Вона супер популярна, але в неї також є недоліки:
  - потрібна реєстрація на порталі розробників
  - створення окремого проекту, який має обмеження по часу використання
  - в мене не вдалось нормально підключити пристрої з `Protocol Version: 3.5`

Довго грався з `Local Tuya`, але нормально налаштувати не вдалось. Деталі можна побачити через GIT історію в файлі `/home-assistant/d-c-old.yml`.

Простіший варіант - інтеграція `HACS` `Tuya Local`.\
Ця інтеграція ще не додана в штатний каталог `HACS`, тому її додаємо в ручну (через пряме посилання на GIT репозиторій).

##### Етапи встановлення:
1. Додаємо: `HACS -> Додаткове меню (...) -> Custom repositories`
    - `Repository: https://github.com/make-all/tuya-local`
    - `Type: Integration`
    - підтверджуємо
2. Встановлюємо:
    - в пошуку `HACS` шукаємо `Tuya Local`
    - переходимо
    - натискаємо `Download`
    - підтверджуємо
3. Перезапускаємо HA
4. Додаємо інтеграцію
    - `Налаштування -> Пристрої та сервіси (Інтеграції) -> Додати інтеграцію -> Tuya Local`
    - Обираємо варіант `Хмарне налаштування пристрою SmartLife.` (`Smart Life cloud-assisted device setup.`)
5. 'Увійти в Tuya'
    - відкриваємо додаток `Tuya` на телефоні
    - вводимо код `Профіль -> Налаштування -> Обліковий запис і безпека -> User Code`
    - з'являється QR-код, скануємо його додатком, підтверджуємо в додатку
    - натискаємо 'Надіслати'
6. 'Виберіть пристрій для додавання'. Тут обираємо сам пристрій, і якщо це `ZigBee` - ще й шлюз через який пристрій має працювати (в системі їх може бути декілька).
7. 'Знайдіть IP-адресу пристрою'. Натискаємо далі.
8. 'Налаштуйте свій пристрій Tuya Local'.
    - `Ідентифікатор пристрою (device_id, або device_id шлюзу для пристроїв, підключених через шлюз): bfda025e72943fdb77gcuv` - підтягнувся сам, якщо потрібно - можна отримати в додатку 'Інформація про пристрій'
    - `IP-адреса або ім'я хоста: 192.168.0.100` - сам не підтягнувся, можна отримати зайшовши в роутер, в списку підключених девайсів (`tomzn_tompd_63wifi`). Фіксуємо в роутері [статичний IP](#2-general-nuances), щоб девайс не відвалювався.
    - `Локальний ключ (local_key): }E4j${<'YMuflk<n` - підтягнувся сам, якщо потрібно - тут жостко, дивитись деталі в GIT історії файлу `/home-assistant/d-c-old.yml`
    - `Версія протоколу (якщо не знаєте, спробуйте 'auto'): auto` - підтягнувся сам, при переході на наступний крок довго думало, пробувало і визначило як 3.5 (до того в логах було 4 помилки типу `ERROR (MainThread) [custom_components.tuya_local.device] Failed to refresh device state for Test.`)
    - інше не змінюємо
9. 'Оберіть тип пристрою' визначається автоматично, якщо щось специфічне - пробуємо обрати вручну
10. 'Налаштування пристрою' - назва пристрою в `HA`

Повторюємо додавання для кожного пристрою через `Tuya Local -> Додати пристрій`.\
Потрібно пройтись по налаштуванням кожного з пристроїв та включити або виключати парамери.

##### нюанси налаштувань:
- Фіксуємо в роутері [статичний IP](#2-general-nuances), щоб девайс не відвалювався.
- Чомусь в `tomzn_tompd_63wifi` не оновлюються дані про 'Живлення' та 'Струм', деталі в [загальних нюансах](#2-general-nuances)

---

#### 5.003. Local Tuya
В `HACS` є інтеграція `Local Tuya` (https://github.com/rospogrigio/localtuya/).

Вона супер популярна, але в неї також є недоліки:
  - потрібна реєстрація на порталі розробників
  - створення окремого проекту, який має обмеження по часу використання
  - в мене не вдалось нормально підключити пристрої з `Protocol Version: 3.5`

Пробуємо налаштувати. Але налаштувати не вдалось. 

##### Етапи встановлення:
1. Встановлюємо:
   - в пошуку `HACS` шукаємо `Local Tuya`
   - переходимо
   - натискаємо `Download`
   - підтверджуємо
2. Перезапускаємо HA
3. Додаємо інтеграцію
    - `Налаштування -> Пристрої та сервіси (Інтеграції) -> Додати інтеграцію -> Local Tuya`
    - з’явиться екранчик налаштування `Cloud API account configuration`
        для отримання цих даних потрібно зареєструватись в `Tuya IoT Platform`,
        створити та налаштувати проект,
        деталі як це зробити описані нижче, тут самі налаштування:
        - https://platform.tuya.com/cloud
        - `API server region: eu`
        - `Client ID: upq3e8snws85wqpkwmn5`
            - `Cloud -> Project Management -> B_14_HA -> Open Project -> Overview -> Access ID/Client ID`
        - `Secret: 839003c5cc7747ce9b6d246d773473b6`
            - `... -> Authorization -> Cloud Authorization`
        - `User ID: eu1770065598764UTYSA`
            - `... -> Devices -> Link App Account`
        - `username: serega.n.v333@gmail.com`
          Це електронна пошта (або номер телефону), на яку ви реєстрували акаунт у мобільному додатку Tuya Smart або Smart Life.

##### Реєстрація та налаштування платформи `Tuya IoT Platform`:
Цей акаунт не є акаунтом `Tuya Smart` (APP додаток). Його потрібно окремо налаштовувати.
    - https://platform.tuya.com/cloud

1. Авторизуємось, деталі в pass.
2. переходимо в розділ `Cloud -> Project Management`
3. Створюємо новий хмарний проект `Create Cloud Project`
4. Вводимо основні дані для створення нового проекту:
    - `Project Name: B_14_HA`
    - `Description: B_14_HA`
    - `Industry: Smart Home`
    - `Development Method: Smart Home`
    - `Data Cente: Smart Home`
      - Ви можете вибрати один або кілька центрів обробки даних, де розгортаються ваші послуги. Але чомусь при створенні можна обрати лише один: `Central Europe Data Center`
   - Тиснемо `Create`
5. Вводимо додаткові дані `Configuration Wizard / Authorize API Services`:
   - тут за замовчуванням вже насетаплені певні сервіси, додав лише `Device Status Notification`
     - `IoT Core`
     - `Authorization Token Management`
     - `Smart Home Basic Service`
     - `Data Dashboard Service`
     - `[Deprecate]Smart Home Scene Linkagee`
     - `Device Status Notification`
   - тиснемо `Authorize`
6. В інтерфейсі з'явився новий проект `B_14_HA`, переходимо на нього `Open Project`
    - в ньому нам знадобиться `Access ID/Client ID: upq3e8snws85wqpkwmn5` 
7. Прив'язуємо наш проект з APP додатком
    - `... -> Devices -> Link App Account`
    - тиснемо `Add App Account -> Tuya App Account Authorization`
    - з'являється вікно з QR кодом
    - заходимо в APP додаток `Профіль -> Сканувати QR -> Підтвердити`
    - в `Tuya IoT` зміниться вікно на `Підтвердження`, інфа про 4 девайса, обираємо варіант `Automatic Link (Recommended)`
    - після цього з'явиться інфа про девайси, закриваємо його і побачимо в вкладці `Link App Account` інфу р оновододаний акаунт
8. Переходимо в вкладку `... -> Devices -> All Devices`, в ній маємо побачити усі девайси які під'єднані в додатку
9. Переходимо в вкладку `... -> Authorization -> Cloud Authorization`, в ній бачимо параметри для авторизації в інтеграції `Local Tuya`

##### Додаємо новий прилад:
1. Заходимо в інтеграцію `LocalTuya integration`
2. `Налаштування -> Add a new device -> Далі`
3. `Configure Tuya device`
    - `Name: TOMZN TOMPD-63WIFI (вхідне реле)` - довільна назва
    - `Host: 192.168.0.100` - сам не підтягнувся, можна отримати зайшовши в роутер, в списку підключених девайсів (`tomzn_tompd_63wifi`). Фіксуємо в роутері [статичний IP](#2-general-nuances), щоб девайс не відвалювався
    - `Device ID: bfda025e72943fdb77gcuv` - можна отримати в додатку `Tuya Smart` або в `Tuya IoT Platform`
    - `Local key: }E4j${<'YMuflk<n` - є декілька варіантів для отримання, деталі нижче
    - `Protocol Version: 3.4`, але так не конфіжиться, бо в девайсах `Protocol Version: 3.5`, а в доступних варіантах максимум `3.4`
    - `Enable debugging for this device (debug must be enabled also in configuration.yaml)` для днбпг режиму
    - `Надіслати`
4. 

##### Отримуємо `Local key`:

Можна отримати в `Tuya IoT Platform`:
1. Переходимо за посиланням: https://eu.platform.tuya.com/cloud/explorer
2. `Device Management -> Query Device Details`
3. Вводимо `device_id: bfda025e72943fdb77gcuv`
4. `Submit Request`
5. Отримуємо JSON де є стрічка `"local_key": "}E4j${<'YMuflk<n",`

Можна отримати за допомогою `tinytuya`:
- Встановлюємо tinytuya:
  - `sudo apt install python3-pip`
  - `sudo apt-get install python3 python3-pip python3-full`
  - `sudo apt-get install python-crypto python-pip`
  - `pip install tinytuya`

- Cтандартним методом встановити додаток не вдалось, тому робимо так:
  - `sudo apt update`
  - `sudo apt install python3-venv`
  - `python3 -m venv tinytuya-env`
  - `source tinytuya-env/bin/activate`
  - `pip install tinytuya`
  - `python3 -m tinytuya wizard`
    - підключаємось до девайса
      ```JSON
        {
          "apiKey": "t85trp5a8cxvedwate95",
          "apiSecret": "8bf7fe82d8404729b04647a6f21fc65c",
          "apiRegion": "eu",
          "apiDeviceID": "bfda025e72943fdb77gcuv"
        }
      ```
  - в корневій папці отримуємо два фійліка:
    - `tinytuya.json` (конфіги налаштувань)
    - `tuya-raw.json` (детальна інфа про девайси)
  - `python3 -m tinytuya scan`
    - скануються девайси
    - в результаті отрмав таку стрічку по девайсу:
      - `Вхідне реле Product ID = u0cxoxtfelys3ufx [Valid Broadcast]: Address = 192.168.0.106 Device ID = bfda025e72943fdb77gcuv (len:22) Local Key = }E4j${<'YMuflk<n Version = 3.5 Type = default, MAC = 3c:0b:59:b1:a5:de Status: {'1': 32802, '9': 0, '11': False, '13': 0, '15': 13, '16': True, '19': '1234', '104': 595, '105': 500, '116': 2176, '117': 2080, '118': 269}`
      - із цікавого - Version = 3.5,
      - я так розумію це 'Protocol Version' з налаштувань підключення в LocalTuya
      - але в списку допустимих протоколів 3.1, 3.2, 3.3, 3.4,
      - тобто Protocol Version - відсутня і я не можу його обрати

---

#### 5.004. Sonoff
Користувацький компонент `Home Assistant` для керування пристроями `Sonoff` з прошивкою `eWeLink` (оригінальною) через локальну мережу та/або хмару. Чомусь поки що не вдається налаштувати через локальну мережу, лише через хмару.\
Простий варіант - інтеграція `HACS` `Sonoff LAN`.\

##### Етапи встановлення:
1. Встановлюємо:
    - в пошуку `HACS` шукаємо `Sonoff`
    - переходимо
    - натискаємо `Download`
    - підтверджуємо
2. Перезапускаємо HA
3. Додаємо інтеграцію
    - `Налаштування -> Пристрої та сервіси (Інтеграції) -> Додати інтеграцію -> Sonoff`
    - з’явиться екранчик налаштування `Sonoff` на введення облікових даних `eWeLink`
    - для отримання цих даних потрібно (деталі в pass):
      - Email або телефон (будь-який для DIY режиму): serega.n.v333@gmail.com
      - Пароль (залишіть порожнім для режиму DIY): 1357908642eE
      - Country code (leave blank for auto select): Ukraine | +380
4. Додаємо пристрій

##### нюанси налаштувань:
- Фіксуємо в роутері [статичний IP](#2-general-nuances), щоб девайс не відвалювався.
- `Sonoff S60TPF` не працює без доступу до хмари, потрібен інтернет.

##### todo:
- `Sonoff S60TPF` налаштувати роботу без доступу до хмари, без інтернету.

---

#### 5.005. Solarman
Користувацький компонент `⚡ Solarman Stick Logger` для керування Wifi логером інвертора `Deye` через локальну мережу та/або хмару. ????????.\
Простий варіант - інтеграція `HACS -> Solarman`.\

##### Етапи встановлення:
1. Встановлюємо:
    - в пошуку `HACS` шукаємо `Solarman`
    - переходимо
    - натискаємо `Download`
    - підтверджуємо
2. Перезапускаємо HA
3. Додаємо інтеграцію
    - `Налаштування -> Пристрої та сервіси (Інтеграції) -> Додати інтеграцію -> Solarman`
    - з’явиться екранчик налаштування `Solarman`
      - `Device name: Deye-SUN-6K`
      - `Hostname or IP address: 192.168.0.106` - сам не підтягнувся, можна отримати зайшовши в роутер, в списку підключених девайсів (`deye_stick_logger_lsw_5`). Фіксуємо в роутері [статичний IP](#2-general-nuances), щоб девайс не відвалювався.
      - `Port: 8899` - сетапиця автоматично, нічого не змінюємо
      - `Transport protocol: TCP` - сетапиця автоматично, нічого не змінюємо
      - `Profile: Auto` - сетапиця автоматично, нічого не змінюємо
      - `Additional options` - нічого не змінюємо
4. Додаємо пристрій

##### нюанси налаштувань:
- Фіксуємо в роутері [статичний IP](#2-general-nuances), щоб девайс не відвалювався.


---

### 6. Setup steps
Етапи налаштування.
1. Запускаємо [Portainer](#301-portainer)
2. Запускаємо [Home Assistant](#302-home-assistant)
3. Запускаємо [MariaDB](#303-mariadb)
4. Запускаємо [DBeaver](#304-dbeaver)
5. Налаштування [Code Server](#305-code-server)
6. Налаштування [HACS](#5001-setup-hacs)
7. Налаштування [Tuya](#5002-tuya)
8. Налаштування [Sonoff](#5004-sonoff)
9. Налаштування [Solarman](#5005-solarman)


10. 

---

### 7. Add menu item
Додавання нового пункту меню:
- `Налаштування -> Інформаційні панелі -> Додати інформаційну панель
-> Веб-сторінка`

Для сторінок з авторизацією можуть бути проблеми.

#### 7.01. Portainer
  - title: Portainer
  - url: http://127.0.0.1:9002/#!/home
  - icon: mdi:docker
  - URL-адреса: dashboard-portainer (формується автоматично, не змінюємо)
  - Admin-only: true
  - Add to sidebar: true

#### 7.02. DBeaver
  - title: DBeaver
  - url: http://127.0.0.1:8978
  - icon: mdi:database-search
  - URL-адреса: dashboard-dbeaver (формується автоматично, не змінюємо)
  - Admin-only: true
  - Add to sidebar: true

#### 7.03. Configurator
  - title: Configurator
  - url: http://127.0.0.1:8443/?folder=/var/www
  - icon: mdi:wrench
  - URL-адреса: dashboard-configurator (формується автоматично, не змінюємо)
  - Admin-only: true
  - Add to sidebar: true

---
