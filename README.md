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
  - [HACS](#306-hacs)
  - [Tuya](#307-tuya)
  - [Sonoff](#308-sonoff)
  - [Solarman](#309-solarman)
- [Setup steps](#4-setup-steps)
- [Add menu item](#5-add-menu-item)

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
#### Загальні нюанси:
- Налаштування приватності проекта https://github.com/seregaNV/home-assistant/settings
  щоб був недоступний іншим юзерам
- Щоб можна було пушати - створюємо Access tocken
  https://github.com/settings/tokens/new
  З вашого облікового запису GitHub перейдіть до `Settings` → `Developer Settings` → `Personal Access Token` → `Tokens (classic)` → `Generate New Token (Give your password)` → Заповніть форму → натисніть Згенерувати токен → буде виглядати приблизно так: `xxx_xxxxxxxxxxx`
    - new_token
    - 90 days
    - repo
- Фіксуємо в роутері статичний IP, щоб девайс не відвалювався.
  - http://tplinkwifi.net/
    - `'Додаткові налаштування' -> 'Мережа' -> 'DHCP-сервер' -> 'Резервування адрес' -> Додати`
  - переіменовуємо девайс, наприклад в `sonoff_s60_tpf_001`, щоб простіше було шукати
    - `'Схема мережі' -> 'Клієнти' -> обираємо необхідний девайс -> переіменовуємо`
- Довго не міг пойняти чому відвалилась моніторилка в TOMZN TOMPD-63WIFI. 'Енергія' оновлювалась приблизно 1 раз на хвилину, а от 'Живлення' та 'Струм' не оновлювалось взагалі, допомогло перезавантаження ноута та самого Home Assistant
  помітив взаємозв'язок між з актуалізацією даних в HA як тільки відкриваю мобільний додаток Tuya - дані про 'Живлення' та 'Струм' в HA починають оновлюватись майже кожну секунду як тільки додаток закриваю - оновлення даних зупиняються як вирішити проблеему з оновленням даних?
- 

---

### 3. Configuration
#### 3.01. Portainer
- command: `docker compose up portainer`
- configs:
  - host: `http://127.0.0.1:9002/`
  - username: `admin`
  - password: `0123456789pP`

##### нюанси налаштувань:
  - `environment: CSP=false` - не секюрно, може бути доступ за межами мережі


#### 3.02. Home Assistant
Домашній помічник – окрема установка основного домашнього помічника.
- command: `docker compose up home-assistant`
- configs:
  - host: `http://127.0.0.1:8123/`
  - name: `B14`
  - username: `serega_b14`
  - password: `3535353535hHaA`

##### Етапи налаштувань:
- Ініціалізація
- Вмикаємо 'Розширений режим'
- Додаємо пункт [меню](#501-portainer)

##### нюанси налаштувань:
- `volumes: /run/dbus:/run/dbus:ro` - Прокидування /run/dbus у Docker-контейнер потрібне,
  щоб контейнер міг підключатися до системної D-Bus шини хоста
  і взаємодіяти з системними сервісами `systemd`, `NetworkManager`, `BlueZ (Bluetooth)`,
  `logind`, `Avahi`, `UPower`, ...
- в самій IDE не відображаються дані з `/home-assistant/data/mariadb/data/ha_db`
  через права доступа, це не критично, якщо потрібно - дивимось в самому контейнері `mariadb` або в `mc`


#### 3.03. MariaDB
Локальна БД
- command: `docker compose up mariadb`
- configs: `env/MARIA.env`

##### Етапи налаштувань:
- Підключаємо MariaDB до Home Assistant


#### 3.04. DBeaver
Віртуальна web-версія DBeaver для роботи з локальною БД
- command: `docker compose up cloudbeaver`
- configs:
    - host: `http://127.0.0.1:8978/`

##### Етапи налаштувань:
- Ініціалізація
- При першому запуску налаштовуємо connection
- New Connection
- Додаємо пункт [меню](#502-dbeaver)

#### 3.05. Code Server
Віртуальна IDE, для можливості правити код на віртуальній машині
- command: `docker compose up code-server`
- configs:
    - host: `http://127.0.0.1:8443/`

##### Етапи налаштувань:
- Ініціалізація
- Додаємо пункт [меню](#503-configurator)


#### 3.06. HACS
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


#### 3.07. Tuya
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

##### todo:
- чомусь в `tomzn_tompd_63wifi` дуже обмежена кількість датчиків, поресьорчити з типами підключення.


#### 3.08. Sonoff
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


#### 3.09. Solarman
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

### 4. Setup steps
1. Налаштування [Portainer](#301-portainer)
   - Запуск
2. Налаштування [Home Assistant](#302-home-assistant)
   - Запуск
   - Ініціалізація
     - 'Створи мій розумний дім'
     - 'Створити користувача' - вводимо дані з `configs`
     - 'Місцезнаходження будинку'
     - 'Допоможіть нам допомогти вам'
     - 'Все готово!'
   - Вмикаємо 'Розширений режим'
     'B14' -> 'Налаштування користувача' -> 'Розширений режим'
     Home Assistant за замовчуванням приховує розширені функції та параметри.
     Ви можете зробити ці функції доступними, перемкнувши цей перемикач.
     Це налаштування орієнтоване на поточного користувача, і не впливає на інших користувачів,
     які використовують Home Assistant.
3. Налаштування [MariaDB](#303-mariadb)
   - Підключаємо MariaDB до Home Assistant
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
4. Налаштування [DBeaver](#304-dbeaver)
  - При першому запуску налаштовуємо connection
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
5. Налаштування [Code Server](#305-code-server)
6. Налаштування [HACS](#306-hacs)
7. Налаштування [Tuya](#307-tuya)
8. Налаштування [Sonoff](#308-sonoff)
9. Налаштування [Solarman](#309-solarman)
10. 

---

### 5. Add menu item
Додавання нового пункту меню.\
`Налаштування -> Інформаційні панелі -> Додати інформаційну панель
-> Веб-сторінка`\
Для сторінок з авторизацією можуть бути проблеми.

#### 5.01. Portainer
  - title: Portainer
  - url: http://127.0.0.1:9002/#!/home
  - icon: mdi:docker
  - URL-адреса: dashboard-portainer (формується автоматично, не змінюємо)
  - Admin-only: true
  - Add to sidebar: true

#### 5.02. DBeaver
  - title: DBeaver
  - url: http://127.0.0.1:8978
  - icon: mdi:database-search
  - URL-адреса: dashboard-dbeaver (формується автоматично, не змінюємо)
  - Admin-only: true
  - Add to sidebar: true

#### 5.03. Configurator
  - title: Configurator
  - url: http://127.0.0.1:8443/?folder=/var/www
  - icon: mdi:wrench
  - URL-адреса: dashboard-configurator (формується автоматично, не змінюємо)
  - Admin-only: true
  - Add to sidebar: true

---
