# Практическое задание по теме «Swagger. Часть 1»

## Постановка задачи 
Кинотеатр “Искорка” показывает фильмы для детей и подростков. Кинотеатр имеет онлайн расписание, а также продает билеты на свои сеансы онлайн через систему бронирования. Информация о забронированных билетах вносится в базу бронирования, с указанием данных покупателя.
Все данные о сеансах и бронировании хранятся и обрабатываются на одном сервере.
Требуется автоматизировать решение следующих задач:
* Узнать наличие мест на сеанс
* Забронировать место
* Отменить бронь
* Изменить время сеанса (администратором кинотеатра)

Так как система построена на микросервисной архитектуре - для решения задачи нужно спроектировать API.
## Допущения
В кинотеатре 1 зал с одинаковым количеством рядов и мест в каждом ряду (иначе нужно делать еще один объект с залами).
Не включена база с фильмами.
## Решение
### Шаг 1. Определение объектов (ресурсов)
1. Расписание / sessions
2. Бронирование / booking
3. Пользователи / customers

**Расписание / sessions**

|Данные|Обозначение|Тип данных|
|-|--------|---|
|Идентификатор|sessionId|integer|
|Дата сеанса|date|string (формат date)|
|Время сеанса|time|string (формат date)|
|Фильм|film|string|
|Цена|price|string|
|Наличие свободных мест|freeSeats|boolean|
|Зал: Ряд (1-7); Место (1-9)|hall: row; seat|array (object):integer, enum (1-7); integer, enum (1-9)|

**Пользователи/ customers**

|Данные|Обозначение|Тип данных|
|-|--------|---|
|Идентификатор|customerId|integer|
|Имя|name|string|
|e-mail|email|string (формат email)|
|телефон|phone|string (формат phone)|

**Бронирования / booking**

|Данные|Обозначение|Тип данных|
|-|--------|---|
|Идентификатор|bookingId|integer|
|Пользователь|customerId|integer|
|Сеанс|sessionId|integer|
|Выбранное место: Ряд (1-7), Место (1-9)|place: row; seat|object: integer, enum (1-7); integer, enum (1-9)|
|Статус: новый, завершен, удален|status: active; done; canceled|string, enum: active; done; canceled|

### Шаг 2. Определение, кто будет взаимодействовать с API
Пользователи приложения кинотеатра “Искорка”
### Шаг 3. Какие действия будет выполнять API и какими методами
* GET / sessions - Посмотреть расписание
* GET / sessions / {sessionId}  - Узнать наличие мест на сеанс
* PATCH / sessions / {sessionId} / time - Изменить время сеанса (администратором кинотеатра)
* POST / booking / {bookingId} - Забронировать место
* GET / booking - Посмотреть все бронирования
* GET / booking / {bookingId} - Посмотреть конкретное бронирование
* DELETE / booking / {bookingId} - Отменить бронь
### Шаг 4. Определить параметры запроса и ответа
#### GET / sessions - Посмотреть расписание
*Параметры запроса:* date; timePeriod; film; freeSeats = true, sort (by date and time ASC)\
*Ответ:* массив объектов (sessionId; date; time; film; price; freeSeats)
#### GET / sessions / {sessionId} - Выбрать место на сеанс
*Параметры запроса:* sessionId\
*Ответ:* объект (sessionId; date; time; film; price; freeSeats; hall (объект (row, seat))
#### PATCH / sessions / {sessionId} / datetime - Изменить время сеанса
*Параметры запроса:* sessionId\
*Тело запроса:* объект (time)\
*Ответ:* объект (sessionId; date; time; film; price)
#### POST / booking / {bookingId} - Забронировать место
*Параметры запроса:* -\
*Тело запроса:* объект (date, time, place)\
*Ответ:* объект (bookingId; customerId; sessionId.date; sessionId.time; sessionId.film; sessionId.price; place)
#### GET / booking - Посмотреть все бронирования
*Параметры запроса:* -\
*Ответ:* объект (bookingId; sessionId.date; sessionId.time; sessionId.film, status)
#### GET / booking / {bookingId} - Посмотреть конкретное бронирование
*Параметры запроса:* bookingId\
*Ответ:* объект (bookingId; sessionId.date; sessionId.time; sessionId.film, sessionId.price; place; status)
#### PATCH / booking / {bookingId} - Отменить бронь
*Параметры запроса:* bookingId\
*Ответ:* объект (bookingId; status = canceled)


