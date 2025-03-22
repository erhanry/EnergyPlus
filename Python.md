# 🚀 Проект за обработка на данни за енергийния пазар


[![Build Status](img/build-passing.svg)](/)
[![Coverage](img/coverage.svg)](/)
[![License: MIT](img/License-MIT.svg)](LICENSE)


## 📌 Описание
Този проект е изпълнен с Python скрипт, който извлича, обработва и записва данни от различни сегменти на енергийния пазар (Auction, DAM, IDM, Imbalance) и ги съхранява в MongoDB.

## 🚀 Функционалности
- Извличане на данни от:
  - **Auction Market** (Бидове за първично и вторично регулиране)
  - **Day Ahead Market (DAM)** (Количества и цени за сегмент 'Ден напред')
  - **Intraday Market (IDM)** (Количества и цени за сегмент 'В рамките на деня')
  - **Imbalance Market** (Прогнозни данни от енергийния оператор 'ЕСО')
- Автоматично преобразуване на времеви зони
- Оптимизирана обработка на данни
- Интеграция с MongoDB за съхранение

---

## 🛠️ Инсталация и настройка

### 💻 Инсталация на Windows

1. **🛠️ Създайте виртуална среда:**
   ```sh
   python -m venv venv
   ```

2. **✅ Активирайте виртуалната среда:**
   ```sh
   venv\Scripts\activate
   ```

3. **📦 Актуализирайте `pip` и инсталирайте зависимостите:**
   ```sh
   python.exe -m pip install --upgrade pip
   pip install -r requirements.txt
   ```

4. **🚀 Стартиране на скриптовете:**
   ```sh
   python -B src/auc.py
   python -B src/dam.py
   python -B src/idm.py
   python -B src/imb.py
   ```

### 🐧 Инсталация на Ubuntu

1. **🛠️ Създайте и активирайте виртуална среда:**
   ```sh
   python3 -m venv venv
   source venv/bin/activate
   ```

2. **📦 Актуализирайте `pip` и инсталирайте зависимостите:**
   ```sh
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

3. **🚀 Стартиране на скриптовете:**
   ```sh
   python -B src/auc.py
   python -B src/dam.py
   python -B src/idm.py
   python -B src/imb.py
   ```

## 🔑 Конфигурация на .env файл

Проектът използва `.env` файл за задаване на конфигурационни параметри. 
Примерен `.env` файл:
```ini
MONGO_URI=mongodb://localhost:27017
```

За да заредите `.env` файла, той трябва да бъде в основната директория на проекта.

## 🐳 Използване с Docker

### 📦 Създаване и стартиране на контейнер

1. **🔹 Инсталирайте Docker и Docker Compose:**
   - Инструкции за [Windows](https://docs.docker.com/desktop/install/windows-install/) и [Ubuntu](https://docs.docker.com/engine/install/ubuntu/).

2. **⚡ Изградете и стартирайте контейнера:**
   ```sh
   docker-compose build --no-cache
   docker-compose up -d
   ```

3. **📜 Проверете логовете:**
   ```sh
   docker logs cron || sudo nano /var/lib/docker/volumes/cronlog/_data/auc.log
   ```

4. **🛑 Спиране на контейнера:**
   ```sh
   docker-compose down
   ```

5. **🛠️ Полезни Docker команди:**
   ```sh
   docker-compose exec app bash  # Влизане в контейнера
   docker-compose logs -f        # Проследяване на логовете в реално време
   docker ps                     # Списък на работещите контейнери
   docker stop <container_id>     # Спиране на конкретен контейнер
   ```

## 📂 Структура на проекта

```
src/
├── api/
│   ├── client.py         # 🌐 HTTP клиент за заявки
│
├── db/
│   ├── mongo.py          # 🗄️ Връзка с MongoDB
│
├── lib/
│   ├── auc_parser.py     # 📊 Парсване на Auction данни
│   ├── dam_parser.py     # 📊 Парсване на DAM данни
│   ├── idm_parser.py     # 📊 Парсване на IDM данни
│   ├── imb_parser.py     # 📊 Парсване на IMB данни
│
├── utils/
│   ├── converter_utils.py # 🕒 Функции за дати и време
│   ├── parser_utils.py    # 🔍 Общи функции за парсване
│
├── auc.py                # ⚡ Скрипт за Auction Market
├── dam.py                # ⚡ Скрипт за Day-Ahead Market
├── idm.py                # ⚡ Скрипт за Intraday Market
├── imb.py                # ⚡ Скрипт за Imbalance Market
```

## 🔍 Анализ на кода с mypy и pylint

- Кодът е форматиран по **PEP-8** стандарта.
- Проектът поддържа анализ на статични типове с `mypy` и проверка за стил с `pylint`.

### 🔎 Проверка с `mypy`:
```sh
mypy src/
```
Ако има грешки, те ще бъдат изведени в терминала и трябва да бъдат коригирани.

### 🔎 Проверка с `pylint`:
```sh
pylint src/
```
Тази команда ще покаже предупреждения и предложения за подобрения в кода.

## 📜 Функции на проекта

| 📂 Файл               | ⚙️ Функция                | 📝 Описание |
|--------------------|-----------------------|----------|
| `src/auc.py`      | `auction_contracts()`  | 📊 Извлича и записва данни за аукционите |
| `src/dam.py`      | `day_ahead_market()`   | 📊 Извлича и записва данни за DAM |
| `src/idm.py`      | `intraday_market()`    | 📊 Извлича и записва данни за IDM |
| `src/imb.py`      | `imbalance_data()`     | 📊 Извлича и записва данни за небаланси |
| `mongo.py`        | `save_to_mongodb()`    | 🗄️ Записва данни в MongoDB |
| `mongo.py`        | `read_from_mongodb()`  | 🗄️ Чете данни от MongoDB |
| `client.py`       | `get_request()`        | 🌐 Изпраща HTTP GET заявки |
| `client.py`       | `post_request()`       | 🌐 Изпраща HTTP POST заявки |
| `converter_utils.py` | `get_current_time()`  | 🕒 Връща текущата дата и час |
| `converter_utils.py` | `convert_float()`     | 🔢 Преобразува текст в число |
| `parser_utils.py` | `parse_html()`         | 🔍 Парсира HTML съдържание |

## 📦 Зависимости

### ⏳ Runtime зависимости (`requirements.txt`)
- `Python 3.8+`
- `beautifulsoup4`
- `pymongo`
- `python-dotenv`
- `pytz`
- `requests`

## ⚖️ Вноски и Развитие

Ако желаете да допринесете към проекта, моля, следвайте тези стъпки:

1. 🍴 **Форкнете репозитория.**
2. 🌿 **Създайте нов branch** с описателно име за вашата промяна.
3. 🛠️ **Извършете промените**, като се уверите, че всички тестове преминават.
4. 📩 **Изпратете pull request** към основния репозитория.

## 📜 Лиценз

Този проект е с отворен код и е достъпен под **MIT License**.

## 📬 Контакти

За въпроси и предложения, моля свържете се на **erhan.ysuf @ gmail.com**.

📅 **Последно обновено:** 2025-03-22
