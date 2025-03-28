# 📌 MongoDB_v4.0.28 Репликация с Docker Compose

## 📖 Съдържание

- [📝 Описание](#-описание)
- [📦 Контейнери](#-контейнери)
- [🔄 Логика на реплика сета](#-логика-на-реплика-сета)
- [🚀 Стартиране на проекта](#-стартиране-на-проекта)
- [❌ Спиране и изтриване на контейнерите](#-спиране-и-изтриване-на-контейнерите)
- [🎨 Дизайн на системата](#-дизайн-на-системата)
- [🛠 Функционалности](#-функционалности)
- [📄 Конфигурация на мрежата](#-конфигурация-на-мрежата)
- [📬 Полезни команди](#-полезни-команди)
- [📜 Лиценз](#-лиценз)
- [📬 Контакти](#-контакти)

## 📝 Описание

Този проект използва **Docker Compose** за създаване на MongoDB Replica Set с **първичен** (Primary), **вторичен** (Secondary) и **арбитър** (Arbiter) възел. Това осигурява **резервираност** и **висока наличност** на базата данни.

## 📦 Контейнери

| Контейнер       | Роля                                        | Порт(ове) | Описание                                                                                 |
| --------------- | ------------------------------------------- | --------- | ---------------------------------------------------------------------------------------- |
| mongo-primary   | Основен нод за записите                     | 27017     | Генерира keyfile и стартира mongod с реплика сет конфигурация.                           |
| mongo-secondary | Вторичен нод, който репликира данни         | 27018     | Стартира mongod на порт 27018 с по-нисък приоритет.                                      |
| mongo-arbiter   | Арбитър за избори (без съхранение на данни) | 27019     | Стартира mongod като арбитър (arbiterOnly: true).                                        |
| mongo-setup     | Контейнер за инициализация                  | -         | Изчаква да е готов mongo-primary и изпълнява командата за инициализация на реплика сета. |

## 🔄 Логика на реплика сета

### 📌 Стартиране на първичния нод:

- `mongo-primary` контейнерът се стартира, генерира `keyfile` и стартира MongoDB със зададения реплика сет.

### 📌 Стартиране на вторичния и арбитър нодовете:

- `mongo-secondary` и `mongo-arbiter` се стартират на съответните си портове, използвайки същия `keyfile` и конфигурация.

### ⚡ Автоматична инициализация:

- `mongo-setup` контейнерът изчаква около **20 секунди**, за да се увери, че първичният нод е готов.
- След това проверява достъпността чрез `ping`.
- Накрая изпълнява командата за инициализация на реплика сета:

```javascript
rs.initiate({
  _id: "<MONGO_REPLICA_SET>",
  members: [
    { _id: 0, host: "mongo-primary:27017", priority: 1 },
    { _id: 1, host: "mongo-secondary:27018", priority: 0.7 },
    { _id: 2, host: "mongo-arbiter:27019", arbiterOnly: true }
  ]
})
```

🚀 Това установява реплика сета с правилните приоритети и настройки.

## 🚀 Стартиране на проекта

1. **Клониране на репозиторито** *(ако е необходимо)*:

   ```sh
   git clone <URL-НА-РЕПОЗИТОРИЯ>
   cd <ИМЕ-НА-ПРОЕКТА>
   ```

2. **Стартиране на контейнерите**:

   ```sh
   docker-compose build --no-cache
   docker-compose up -d
   ```

3. **Проверка на работещите контейнери**:

   ```sh
   docker ps
   ```

4. **Проверка на MongoDB Replica Set**:

   ```sh
   docker exec -it mongo_primary mongo --eval 'rs.status()'
   rs.config()
   db.version()
   ```

## ❌ Спиране и изтриване на контейнерите

За да **спрете** всички работещи контейнери:

```sh
docker-compose down
```

За да **изтриете всички съхранени данни, контейнери, мрежа и имидж**:

```sh
docker volume rm mongo_primary mongo_secondary mongo_arbiter mongo_keyfile mongo_setup
docker rm -f mongo_primary mongo_secondary mongo_arbiter mongo_keyfile mongo_setup
docker network rm energy
docker rmi -f erhanbg/mongodb:4.0.28
```

## 🎨 Дизайн на системата

MongoDB Replica Set се състои от **три възела**, което осигурява **резервираност** и **устойчивост**:

- **Основен възел (Primary)** - Обработва всички операции по запис.
- **Вторичен възел (Secondary)** - Репликира данните и може да стане Primary при нужда.
- **Арбитър (Arbiter)** - Участва в изборите за нов Primary, но не съхранява данни.

🛡 *Тази архитектура осигурява защита срещу откази на основния сървър.*

## 🛠 Функционалности

- **Сигурност 🔒**: Използва се генериран `keyfile` за автентикация между нодовете.
- **Автоматична инициализация 🤖**: Контейнерът `mongo-setup` изчаква да е готов първичният нод и след това изпълнява командата за инициализация на реплика сета.
- **Постоянно съхранение на данни 💾**: Docker volume-ите гарантират, че данните не се губят при рестартиране.
- **Персонализирана мрежа 🌐**: Всички контейнери са свързани към специално създадена мрежа `energy` с дефиниран подсет.
- **Лесна поддръжка и почистване 🧹**: Ясни инструкции за билд, стартиране и премахване на инсталацията.

## 📄 Конфигурация на мрежата

Контейнерите работят в потребителска мрежа с **bridge** драйвер и конфигурирана подмрежа `172.10.5.0/24`.

## 📬 Полезни команди

🔹 **Преглед на използваното пространство от Docker:**

```sh
docker system df -v
```

🔹 **Изчистване на неизползвани билдове:**

```sh
docker builder prune -a --force
```

🔹 **Влизане в MongoDB CLI:**

```sh
docker exec -it mongo_primary mongo
```

🔹 **Преглед на логовете на контейнер:**

```sh
docker logs mongo_primary
```

🔹 **Рестартиране на контейнер:**

```sh
docker restart mongo_primary
```

## 📜 Лиценз

Този проект е с отворен код и е достъпен под **MIT License**.

## 📬 Контакти

За въпроси и предложения, моля свържете се на **erhan.ysuf @ gmail.com**.

📅 **Последно обновено:** 2025-03-22
