---
permalink: /webcheckout
---

[![providers](https://img.shields.io/badge/doc-Providers-success)](https://shafizod.github.io/providers)
[![agents](https://img.shields.io/badge/doc-Agents-success)](https://shafizod.github.io/agents)
[![invoices](https://img.shields.io/badge/doc-Invoices-success)](https://shafizod.github.io/invoices)
[![webcheckout](https://img.shields.io/badge/doc-Webcheckout-success)](https://shafizod.github.io/webcheckout)

## Спецификация протокола взаимодействия между системой «alif.pro» и агентами по приему платежей с web ресурсов

## Общие принципы протокола

Передача данных между «alif.pro» (далее Алиф) и агентами по приёму платежей (далее Партнер) построена на основе протокола HTTPS (RFC 2818: HTTP/1.1). 

Для передачи данных используется метод POST – поток данных передается в теле запроса, отправляемого Партнером Алифу.

key и password - логин и пароль пользователя Партнера. Генерируются и передаются приватно Партнеру со стороны Алиф. В рамках данной документации используются следующие данные: key="44444444", password="cztef62wrwcysyubbbdnhlk1rs2cztfsqgwww7j0". Пароль перед использованием нужно хешировать алгоритмом HMAC-SHA256: password = hash_hmac("sha256", password, key) = hash_hmac("sha256", "cztef62wrwcysyubbbdnhlk1rs2cztfsqgwww7j0", "44444444") = "3a60036f4a425d879a3f4708c3a1a2b333ca361a1685a7d91d3a4b6183ae2457"

## Организация протокола

Взаимодействие Алиф и Партнера происходит следующим образом:
1. Партнер формирует запрос в формате формы HTML, включая в него все необходимые данные, и передает его по протоколу HTTPS на сервер Алифа по адресу https://alifpay.tj/web.
2. Сервер осуществляет авторизацию клиента и разбор формы.
3. В случае успешной авторизации сервер выполняет операцию снятия с карты и отправит колбек в формате JSON, в том числе результат выполнения запроса (ОК или код ошибки в случае ошибки).

## Запрос отправки формы

**Параметры запроса**

Параметр        | Тип           | Значение      | Условие       | Описание
--------------- | ------------- | ------------- | ------------- | --------
key             | string        | numeric       | Обязательно   | Логин и пароль пользователя Партнера. Генерируются и передаются приватно Партнеру со стороны Алиф.
token           | string        | hexadecimal   | Обязательно   | Аутентификационный токен, генерируется Партнером по алгоритму SHA256 из конкатенации строк: key+orderId+amount.fixed(2)+сallbackUrl посредством пароля пользователя Партнера.
orderId         | string        | alpha numeric | Обязательно   | Уникальный номер заказа. Генерируется и передается со стороны Партнёра. Используется также для проверки статуса платежа.
amount          | string        | numeric       | Обязательно   | Сумма, вводимая плательщиком в интерфейсе продавца (в сомони).
callbackUrl     | string        | URL           | Обязательно   | URL, по которому Алиф ответить о статусе платежа.
returnUrl       | string        | URL           | Обязательно   | URL, по которому можно вернуться к Торговцу.
phone           | string        | phone         | Обязательно   | Номер телефона отправителя платежа.
info            | string        | string        | Опционально   | Информация о платеже, например: "Оплата за телефон".
email           | string        | email         | Опционально   | Адрес электронной почты отправителя платежа.
infoHash        | string        | hexadecimal   | Опционально   | Аутентификационный токен при оплате услуг налога

```php
<?php
  $key = '44444444';
  $password = 'cztef62wrwcysyubbbdnhlk1rs2cztfsqgwww7j0';
  $callbackUrl = 'http://myshop.tj/thank_you.php';
  $returnUrl = 'http://myshop.tj';
  $amount = 2.99;
  $orderId = '321123';
  $info = 'Xiaomi Mi Mix 2S 6/64 Gb';
  $email = 'testUsers@gmail.com';
  $phone = '988888888';
  $token = hash_hmac('sha256', $key.$orderId.number_format($amount, 2, '.', '').$callbackUrl, hash_hmac('sha256', $key, $password));
?>

<form name="AlifPayForm" action="https://alifpay.tj/web" method="post" id="alifPayForm">
  <input type="hidden" name="key" id="key" value="<?php echo $key;?>">
  <input type="hidden" name="token" id="token" value="<?php echo $token;?>">
  <input type="hidden" name="callbackUrl" id="callbackUrl" value="<?php echo $callbackUrl;?>">
  <input type="hidden" name="returnUrl" id="returnUrl" value="<?php echo $returnUrl;?>">
  <input type="hidden" name="amount" id="amount" value="<?php echo $amount;?>">
  <input type="hidden" name="orderId" id="orderId" value="<?php echo $orderId;?>">
  <input type="hidden" name="info" id="info" value="<?php echo $info;?>">
  <input type="hidden" name="email" id="email" value="<?php echo $email;?>">
  <input type="hidden" name="phone" id="phone" value="<?php echo $phone;?>">
  <input type="submit" value="Пардохт бо Корти Милли">
</form>
```

## Колбек

**Параметры запроса**

Параметр        | Тип           | Значение      | Условие       | Описание
--------------- | ------------- | ------------- | ------------- | --------
orderId         | string        | alpha numeric | Обязательно   | Номер заказа, по которому вы отправили запрос на проведение платежа
transactionId   | string        | alpha numeric | Обязательно   | Уникалный ID транзакции, генерируется Алифом и передается Партнёру.
status          | string        | alpha numeric | Обязательно   | Статус платежа: "ok" или "failed"
token           | string        | hexadecimal   | Обязательно   | Аутентификационный токен для проверки валидности ответа. Генерируется Алифом по алгоритму SHA256 из конкатенации строк: orderId+status+transactionId посредством пароля пользователя Партнера.
amount          | float         | numeric       | Обязательно   | Сумма платежа (в сомони).
phone           | string        | phone         | Обязательно   | Номер телефон плательщика

```json
Метод: POST
Адрес: callbackUrl
Accept: application/json
Content-type: application/json; charset=utf-8
Service-Name: Alifpay

{
  "orderId": "12345678",
  "transactionId": "92938922",
  "status": "ok",
  "token": "75fa87340a0c43a9a0efe9e1aa65f5cab7912e3001714827a5fd481f2d7e0416",
  "amount": 10,
  "phone": "+992931234455"
}

// Конкатенированная строка для генерации token ответа: 12345678ok92938922
```

## Проверка статуса оплаты

**Параметры запроса**

Параметр        | Тип           | Условие       | Описание
--------------- | ------------- | ------------- | ------------- | --------
orderId         | string        | Обязательно   | Номер заказа, по которому вы отправили запрос на проведение платежа
key             | string        | Обязательно   | Генерируется и передается приватно со стороны Алиф.
token           | string        | Обязательно   | Аутентификационный токен. Генерируется Алифом по алгоритму SHA256 из конкатенации строк: key+orderId посредством пароля пользователя Партнера.

**Параметры ответа**

Параметры ответа идентичны с параметрами запроса колбека

```markdown
// Пример запроса
Метод: POST
Адрес: https://alifpay.tj/web/checktxn
Accept: application/json
Content-type: application/json; charset=utf-8

{
  "orderId": "12345678",
  "key": "334122",
  "token": "d7e798553d8db0edfc922dafbd31e246c1d8dd755c62a4da8a9cdc1eb8333d4b"
}

// Конкатенированная строка для генерации token запроса: 33412212345678

// Пример ответа

{
  "orderId": "12345678",
  "transactionId": "92938922",
  "status": "ok",
  "token": "75fa87340a0c43a9a0efe9e1aa65f5cab7912e3001714827a5fd481f2d7e0416",
  "amount": 10,
  "phone": "+992931234455"
}

// Конкатенированная строка для генерации token ответа: 12345678ok92938922
```
