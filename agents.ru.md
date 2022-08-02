---
permalink: /agents
---

[![providers](https://img.shields.io/badge/doc-Providers-success)](https://shafizod.github.io/providers)
[![agents](https://img.shields.io/badge/doc-Agents-success)](https://shafizod.github.io/agents)
[![invoices](https://img.shields.io/badge/doc-Invoices-success)](https://shafizod.github.io/invoices)
[![webcheckout](https://img.shields.io/badge/doc-Webcheckout-success)](https://shafizod.github.io/webcheckout)

[![ru](https://img.shields.io/badge/lang-ru-red.svg)](https://shafizod.github.io/agents)
[![en](https://img.shields.io/badge/lang-en-blue.svg)](https://shafizod.github.io/agents/en)

## Общие принципы протокола

Взаимодействие Агента (далее Партнер) и Системы (далее Алиф) построено на основе протокола HTTPS (RFC 2818: HTTP/1.1) в режиме "запрос-ответ", где инициатором запроса всегда является Партнер, а отвечающей стороной – Алиф. Все запросы должны быть отправлены с методом POST.

Каждый платеж Партнера имеет уникальный идентификатор, который передается в каждом запросе. По идентификатору Партнера производится дальнейшая сверка взаиморасчетов и решение спорных вопросов.

При обработке запроса от Партнера, Алиф выполняет требуемую операцию, а затем ответом передает Партнеру данные и результат выполнения операции.

## Организация протокола

Взаимодействие Партнера и Алиф происходит следующим образом:

1. Партнер формирует запрос в формате JSON, включая в него необходимые данные, и передает его по протоколу HTTPS Алифу по адресу https://alifpay.tj/gate/check для проверки счета клиента, а также создания платежа. Алиф осуществляет авторизацию клиента и разбор JSON. В случае успешной авторизации Алиф выполняет необходимые действия и возвращает Партнеру ответ в формате JSON, включающий результат выполнения запроса. Если Партнер получит успешный ответ ("code":200, "status":"accepted"), то может перейти к следующему шагу (2).

2. После получения успешного ответа от запроса Check, необходимо подтвердить платеж отправив запрос по адресу https://alifpay.tj/gate/pay. Партнер перейдет к следующему шагу (3), если получит нефинальный ответ ("statusCode":2)

3. При получении нефинального ответа от запроса Pay, необходимо отправлять запрос Проверки статуса платежа по адресу https://alifpay.tj/gate/post_check с интервалом в 5 минут.

4. Если Партнер повторит пункт (1) с теми же данными, то получит ответ: "code":409, "status":"{статус платежа в Алифе}". После получения такого ответа к следующему шагу (2) можно перейти только в том случае, если статус платежа в Алифе равен "accepted".

5. Если Партнер повторит пункт (2) с теми же данными, получит ответ: "code":406, "status":"{статус платежа в Системе Алифа}". После получения такого ответа, если статус платежа не финальный (финальные статусы: success, failed, canceled), нужно перейти к шагу (3).

6. Кроме этих запросов есть еще необязательный запрос Проверки счета получателя по адресу https://alifpay.tj/gate/accounts. При получении запроса accounts Алиф проверит по указанной услуге наличие абонента с указанным идентификатором. Признаком успешности данного запроса является "code":200.

## Безопасность

1. Интерфейс должен принимать запросы с IP адресов Партнёра, которые были предварительно переданы техническим сотрудникам Алиф.

2. userid и password - ID и пароль пользователя Партнера. Генерируются и передаются приватно Партнеру со стороны Алиф. В рамках данной документации используются следующие данные: userid="476a1b42-b3dc-40e9-afad-4aaae1d640b9", password="cztef62wrwcysyubbbdnhlk1rs2cztfsqgwww7j0".

3. В запросах используется Аутентификационный токен (hash), который является по сути подписью запроса. Этот hash будет использоваться во всех запросах как часть процесса аутентификации и состоит из хэшированной строки в результате применения алгоритма HMAC-SHA256. Параметрами алгоритма являются Строка и пароль пользователя Партнера.

4. Срока для генерации hash состоит из конкатенации определенных полей и отличается в разных запросах. В обяснении параметров каждого запроса приводится формула конкатенации этой Строки.

## Запросы check, pay, post_check

**Параметры запроса**

Параметр          | Тип           | Условие       | Описание
----------------- | ------------- | ------------- | --------
service         	| string        | Обязательно   | Тип услуги. Смотрите в Таблице №3.
userid          	| string        | Обязательно   | ID пользователя Партнера.
hash            	| string        | Обязательно   | Подпись запроса. Генерируется по алгоритму SHA256 из конкатенации строк: userid+account+txnid+amount.fixed(2) посредством пароля пользователя Партнера.
account         	| string        | Обязательно   | Номера кредита/депозита/карты/кошелка/счёта клиента для пополнения.
amount          	| float         | Обязательно   | Сумма платежа в валюте Партнера либо услуги.
currency        	| string        | Обязательно   | Код валюты по стандарту ISO 4217, например TJS, RUB, USD, UZS, ...
txnid           	| string        | Обязательно   | Уникальный номер транзакции на стороне Партнера. В запросах check и pay одного платежа должен быть одинаковым.
phone           	| string        | Обязательно   | Номер телефона отправителя платежа. Этот номер используется для извещения плательщика или последующего контактирования.
fee             	| float         | Опционально   | Комиссия с клиента, которую взымает партнер.
providerId      	| int           | Условно       | Идентификатор услуги провайдера. Партнеру предоставляется список доступных услуг провайдеров. Обязательно, если "service":"provider".
last_name        	| string        | Условно       | Фамилия отправителя. Обязательно, если "service":"transfer_by_phone \| transfer_by_phone_uz \| card_humouz \| card_uzcard \| card_visa_foreign".
first_name       	| string        | Условно       | Имя отправителя. Обязательно, если "service":"transfer_by_phone \| transfer_by_phone_uz \| card_humouz \| card_uzcard \| card_visa_foreign".
middle_name      	| string        | Опционально   | Отчество отправителя.
sender_birthday  	| string        | Условно       | Дата рождения отправителя в формате DD.MM.YYYY. Обязательно, если "service":"transfer_by_phone \| transfer_by_phone_uz \| card_humouz \| card_uzcard".
id_series_number  | string        | Условно       | Номер документа отправителя. Обязательно, если "service":"transfer_by_phone \| transfer_by_phone_uz".
address         	| string        | Условно       | Обязательно, если "service":"card_visa_foreign".
resident_city    	| string        | Условно       | Обязательно, если "service":"card_visa_foreign".
resident_country 	| int           | Условно       | Обязательно, если "service":"card_visa_foreign".
postal_code      	| string        | Условно       | Обязательно, если "service":"card_visa_foreign".
recipient_name   	| string        | Условно       | Обязательно, если "service":"card_visa_foreign".

**Параметры ответа**

Параметр          | Тип           | Условие       | Описание
----------------- | ------------- | ------------- | --------
id                | int           | Обязательно   | Уникальный идентификатор платежа на стороне Алифа.
datetime          | string        | Обязательно   | Дата и время обработки запроса в формате RFC3339Nano
code              | int           | Обязательно   | Числовой код ответа. Смотрите в Таблице №1
message           | string        | Обязательно   | Описание кода ответа.
status            | string        | Обязательно   | Описание статуса платежа.
statusCode        | int           | Обязательно   | Код статуса. Смотрите в Таблице №2.
amount            | string        | Обязательно   | Зачислямая сумма в валюте услуги.
fx                | string        | Обязательно   | Курс валюты.
topay             | object        | Условно       | Информация по кредитным линиям пользователя. Используется для вывода информации Пользователю и организации выбора Пользователем определенного кредита (транша) в процессе его оплаты. У каждого кредита (транша) есть свой номер и может быть от 01 до 999. Обязательно, если "status":"accepted", "service":"credit".
accountInfo       | string        | Опционально   | Информация о счета получателя.
limit             | string        | Условно       | Остаток лимита пополнения карты клиента. Обязательно, если "service":"card \| card_all"

{::options parse_block_html="true" /}

Пример №1. Пополнение кошелька алиф.моби

<details><summary markdown="span">Check</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/check
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "service": "wallet",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "hash": "a8f29ce5a92dd38b799b72fafc648e719241ee7cda6b9be3f6761de26250d6a7",
    "account": "+992933507769",
    "amount": 80.00,
    "currency": "TJS",
    "txnid": "193342620",
    "phone": "+992935141010",
    "fee": 0.15,
    "providerId": 0,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9+99293350776919334262080.00"

// Ответ

{
    "id": 14477041,
    "datetime": "2022-08-02T10:27:44.289030055+05:00",
    "code": 200,
    "message": "Б.Р.",
    "status": "accepted",
    "statusCode": 0,
    "amount": "80",
    "fx": "1",
    "topay": null,
    "accountInfo": "{\"verified\":true}"
}
```

</details>

<details><summary markdown="span">Pay</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/pay
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "service": "wallet",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "hash": "a8f29ce5a92dd38b799b72fafc648e719241ee7cda6b9be3f6761de26250d6a7",
    "account": "+992933507769",
    "amount": 80.00,
    "currency": "TJS",
    "txnid": "193342620",
    "phone": "+992935141010",
    "fee": 0.15,
    "providerId": 0,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9+99293350776919334262080.00"

// Ответ

{
    "id": 14477041,
    "datetime": "2022-08-02T10:27:44.579560112+05:00",
    "code": 200,
    "message": "payment reached final state",
    "status": "success",
    "statusCode": 1,
    "amount": "80",
    "fx": "1",
    "topay": null,
    "accountInfo": null
}
```

</details>
<br />
Пример №2. Пополнение кредита Алиф

<details><summary markdown="span">Check</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/check
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "account": "14623.00",
    "amount": 160.00,
    "currency": "TJS",
    "hash": "f88ab6fca84e103a02db3e6aec2313237229dea898c551002ce8e05b033f7d35",
    "phone": "931010553",
    "providerId": 0,
    "service": "credit",
    "txnid": "02081025022945",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b914623.0002081025022945160.00"

// Ответ

{
    "id": 14476969,
    "datetime": "2022-08-02T10:25:02.824117951+05:00",
    "code": 200,
    "message": "Салом Ҷ.Т.Г. Мо ин пардохтро ҳамчун транши 14623 қабул менамоем. Қарзи ҳозираатон 158 c.",
    "status": "accepted",
    "statusCode": 0,
    "amount": "160",
    "fx": "1",
    "topay": [
        {
            "id": "00",
            "info": "Салом Ҷ.Т.Г. Мо ин пардохтро ҳамчун транши 14623 қабул менамоем. Қарзи ҳозираатон 158 c."
        }
    ],
    "accountInfo": "{}"
}
```

</details>

<details><summary markdown="span">Pay</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/pay
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "account": "14623.00",
    "amount": 160.00,
    "currency": "TJS",
    "hash": "f88ab6fca84e103a02db3e6aec2313237229dea898c551002ce8e05b033f7d35",
    "phone": "931010553",
    "providerId": 0,
    "service": "credit",
    "txnid": "02081025022945",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b914623.0002081025022945160.00"

// Ответ

{
    "id": 14476969,
    "datetime": "2022-08-02T10:25:03.309168522+05:00",
    "code": 200,
    "message": "payment reached final state",
    "status": "success",
    "statusCode": 1,
    "amount": "160",
    "fx": "1",
    "topay": null,
    "accountInfo": null
}
```

</details>
<br />
Пример №3. Пополнение карты Корти Милли

<details><summary markdown="span">Check</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/check
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "account": "5058270280015610",
    "amount": 655.57,
    "currency": "USD",
    "hash": "de7e305c78f58bbbe8f9588f4c01cd3c17c4b2b61017ac90cc957cf7143547e1",
    "phone": "79627204819",
    "providerId": 0,
    "service": "card_all",
    "txnid": "A3563139401",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b95058270280015610A3563139401655.57"

// Ответ

{
    "id": 14477338,
    "datetime": "2022-08-02T10:38:34.94206247+05:00",
    "code": 200,
    "message": "O. A.",
    "status": "accepted",
    "statusCode": 0,
    "amount": "6660.59",
    "fx": "10.16",
    "topay": null,
    "accountInfo": "{}"
}
```

</details>

<details><summary markdown="span">Pay</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/pay
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "account": "5058270280015610",
    "amount": 655.57,
    "currency": "USD",
    "hash": "de7e305c78f58bbbe8f9588f4c01cd3c17c4b2b61017ac90cc957cf7143547e1",
    "phone": "79627204819",
    "providerId": 0,
    "service": "card_all",
    "txnid": "A3563139401",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b95058270280015610A3563139401655.57"

// Ответ

{
    "id": 14477338,
    "datetime": "2022-08-02T10:38:36.313772759+05:00",
    "code": 200,
    "message": "payment saved for further process",
    "status": "pending",
    "statusCode": 2,
    "amount": "6660.59",
    "fx": "10.16",
    "topay": null,
    "accountInfo": null
}
```

</details>

<details><summary markdown="span">Post Check</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/post_check
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "account": "5058270280015610",
    "amount": 655.57,
    "currency": "USD",
    "hash": "de7e305c78f58bbbe8f9588f4c01cd3c17c4b2b61017ac90cc957cf7143547e1",
    "phone": "79627204819",
    "providerId": 0,
    "service": "card_all",
    "txnid": "A3563139401",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b95058270280015610A3563139401655.57"

// Ответ

{
    "id": 14477338,
    "datetime": "2022-08-02T10:38:37.080271101+05:00",
    "code": 200,
    "message": "Платеж существует",
    "status": "success",
    "statusCode": 1,
    "amount": "6660.59",
    "fx": "10.16",
    "topay": null,
    "accountInfo": null
}
```

</details>
<br />
Пример №4. Пополнение услуги провайдера

<details><summary markdown="span">Check</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/check
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "service": "provider",
    "providerId": 93,
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "hash": "bbcaac2cd9735437a1e93e57c39927d980337927b077b11c41dad6f8bcf43a08",
    "account": "939145566",
    "amount": 372.30,
    "currency": "RUB",
    "phone": "988844074",
    "txnid": "210000617795814",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9939145566210000617795814372.30"

// Ответ

{
    "id": 14477737,
    "datetime": "2022-08-02T10:53:00.823410913+05:00",
    "code": 200,
    "message": "Аккаунт найден",
    "status": "accepted",
    "statusCode": 0,
    "amount": "60.76",
    "fx": "0.1632",
    "topay": null,
    "accountInfo": "{}"
}
```

</details>

<details><summary markdown="span">Pay</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/pay
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "service": "provider",
    "providerId": 93,
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "hash": "bbcaac2cd9735437a1e93e57c39927d980337927b077b11c41dad6f8bcf43a08",
    "account": "939145566",
    "amount": 372.30,
    "currency": "RUB",
    "phone": "988844074",
    "txnid": "210000617795814",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9939145566210000617795814372.30"

// Ответ

{
    "id": 14477737,
    "datetime": "2022-08-02T10:53:39.087346623+05:00",
    "code": 200,
    "message": "payment saved for further process",
    "status": "pending",
    "statusCode": 2,
    "amount": "60.76",
    "fx": "0.1632",
    "topay": null,
    "accountInfo": null
}
```

</details>

<details><summary markdown="span">Post Check</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/post_check
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "service": "provider",
    "providerId": 93,
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "hash": "bbcaac2cd9735437a1e93e57c39927d980337927b077b11c41dad6f8bcf43a08",
    "account": "939145566",
    "amount": 372.30,
    "currency": "RUB",
    "phone": "988844074",
    "txnid": "210000617795814",
    "fee": 0.15,
    "last_name":"Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "sender_birthday": "12.12.1990",
    "id_series_number": "5436271612",
    "address": "Foteh Niyozi St",
    "resident_city": "Dushanbe",
    "resident_country": 860,
    "postal_code": "734000",
    "recipient_name": "John Doe"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9939145566210000617795814372.30"

// Ответ

{
    "id": 14477737,
    "datetime": "2022-08-02T10:54:15.482274805+05:00",
    "code": 200,
    "message": "Платеж существует",
    "status": "success",
    "statusCode": 1,
    "amount": "60.76",
    "fx": "0.1632",
    "topay": null,
    "accountInfo": null
}
```

</details>
<br />
{::options parse_block_html="false" /}

## Запрос accounts

**Параметры запроса**

Параметр          | Тип           | Условие       | Описание
----------------- | ------------- | ------------- | --------
service         	| string        | Обязательно   | Тип услуги. Смотрите в Таблице №3.
userid          	| string        | Обязательно   | ID пользователя Партнера.
hash            	| string        | Обязательно   | Подпись запроса. Генерируется по алгоритму SHA256 из конкатенации строк: userid+':'+datetime посредством пароля пользователя Партнера.
account         	| string        | Обязательно   | Номера кредита/депозита/карты/кошелка/счёта клиента для пополнения.
amount          	| float         | Обязательно   | Сумма платежа в валюте Партнера либо услуги.
currency        	| string        | Обязательно   | Код валюты по стандарту ISO 4217, например TJS, RUB, USD, UZS, ...
providerId      	| int           | Условно       | Идентификатор услуги провайдера. Партнеру предоставляется список доступных услуг провайдеров. Обязательно, если "service":"provider".
datetime          | string        | Обязательно   | Дата и время запроса в формате RFC 7231

**Параметры ответа**

Параметр          | Тип           | Условие       | Описание
----------------- | ------------- | ------------- | --------
code              | int           | Обязательно   | Числовой код ответа. Смотрите в Таблице №1
message           | string        | Обязательно   | Описание кода ответа.
amount            | string        | Обязательно   | Зачислямая сумма в валюте услуги.
fx                | string        | Обязательно   | Курс валюты.
topay             | object        | Условно       | Информацию по кредитным линиям пользователя. Обязательно, если "status":"accepted", "service":"credit".
accountInfo       | string        | Опционально   | Информация о счета получателя.


{::options parse_block_html="true" /}

Пример №1. Проверка кошелька алиф.моби

<details><summary markdown="span">Check Account</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/accounts
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "service": "wallet",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "hash": "eb549c288dea5172a7b21d96402941efdcc5ffe9e713d14c3b16a6625ec95c4f",
    "account": "+992933507769",
    "amount": 80.00,
    "currency": "TJS",
    "providerId": 0,
    "datetime": "Tue, 02 Aug 2022 13:33:26 +05"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9:Tue, 02 Aug 2022 13:33:26 +05"

// Ответ

{
    "code": 200,
    "message": "Операция успешно выполнена",
    "accountInfo": "{\"verified\":true}",
    "topay": null,
    "amount": "80",
    "currency": "TJS",
    "fx": "1"
}
```

</details>
<br />
Пример №2. Проверка кредита Алиф

<details><summary markdown="span">Check Account</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/accounts
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "account": "14623.00",
    "amount": 160.00,
    "currency": "TJS",
    "hash": "3f6fa4c6c4a6576923761a021e69a9c1f46c3797bfa165b7ee170f0fe1db3623",
    "providerId": 0,
    "service": "credit",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "datetime": "Tue, 02 Aug 2022 13:32:48 +05"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9:Tue, 02 Aug 2022 13:32:48 +05"

// Ответ

{
    "code": 200,
    "message": "Салом Ҷ.Т.Г. Мо ин пардохтро ҳамчун транши 14623 қабул менамоем. Қарзи ҳозираатон 158 c.",
    "amount": "160",
    "currency": "TJS",
    "fx": "1",
    "topay": [
        {
            "id": "00",
            "info": "Салом Ҷ.Т.Г. Мо ин пардохтро ҳамчун транши 14623 қабул менамоем. Қарзи ҳозираатон 158 c."
        }
    ],
    "accountInfo": "{}"
}
```

</details>
<br />
Пример №3. Проверка карты Корти Милли

<details><summary markdown="span">Check Account</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/accounts
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "account": "5058270280015610",
    "amount": 655.57,
    "currency": "USD",
    "hash": "03b35c8903854d026b95ec9a5a9eb57e127164f58071713bacf0872524237b3d",
    "providerId": 0,
    "service": "card_all",
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "datetime": "Tue, 02 Aug 2022 08:38:14 +05"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9:Tue, 02 Aug 2022 08:38:14 +05"

// Ответ

{
    "code": 200,
    "message": "Успешно",
    "amount": "6660.59",
    "currency": "TJS",
    "fx": "10.16",
    "topay": null,
    "accountInfo": ""
}
```

</details>
<br />
Пример №4. Проверка счета по услуге провайдера

<details><summary markdown="span">Check Account</summary>

```markdown
// Запрос

Метод: POST
Адрес: https://alifpay.tj/gate/accounts
Accept: application/json
Content-type: application/json; charset=utf-8

{
    "service": "provider",
    "providerId": 93,
    "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
    "hash": "d745524110e68c7f7ed4d945a8a5df4952e15434076952e00aa2fb606ade2250",
    "account": "939145566",
    "amount": 372.30,
    "currency": "RUB",
    "datetime": "Tue, 02 Aug 2022 13:42:49 +05"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9:Tue, 02 Aug 2022 13:42:49 +05"

// Ответ

{
    "code": 200,
    "message": "Успешно",
    "amount": "60.76",
    "currency": "TJS",
    "fx": "0.1632",
    "topay": null,
    "accountInfo": ""
}
```

</details>
<br />
{::options parse_block_html="false" /}


## Таблица №1. Коды ответов

Код |	Описание                                    | Фатальность | check | pay | post_check
--- | ------------------------------------------- | ----------- | ----- | --- | ----------
200	|	Успешно                                     | Да          | +     | +   | +
285	|	Произошла ошибка при конвертации            | Да          | +     | +   | -
286	|	Курс валюты изменился                       | Да          | -     | +   | -
400	|	Неверный запрос                             | Да          | +     | +   | +
401	|	Не авторизован                              | Да          | +     | +   | +
402	|	Получатель не найден                        | Да          | +     | -   | -
403	|	Нет доступа                                 | Да          | +     | +   | +
404	|	Платеж не найден                            | Да          | -     | +   | +
405	|	Метод не разрешен                           | Да          | +     | +   | +
406	|	Повторное подтверждение платежа             | Да          | -     | +   | -
409	|	Повторный запрос проверки                   | Да          | +     | -   | -
410	|	Неверный счет получателя                    | Да          | +     | +   | -
411	|	Сумма слишком мала                          | Да          | +     | -   | -
412	|	Сумма слишком велика                        | Да          | +     | -   | -
413	|	Неверная сумма перевода                     | Да          | +     | +   | -
414	|	Неверный идентификатор запроса              | Да          | +     | +   | +
415	|	Клиент в стоп-листе                         | Да          | +     | -   | -
500	|	Внутренняя ошибка сервера                   | Да          | +     | +   | +
503	|	Временная ошибка. Повторите запрос позже    | Нет         | +     | +   | +
520	|	Платеж в ожидании                           | Нет         | -     | +   | -
521	|	Платеж на проверке                          | Нет         | -     | +   | -

## Таблица №2. Коды статусов

Код   | Статус    | Описание                            |	Фатальность
----- | --------- | ----------------------------------- | -----------
0	    | accepted	| Транзакция принята, ожидание оплаты	| Нет
1	    | success	  | Транзакция прошла успешно           | Да
2	    | pending	  | Транзакция обрабатывается           | Нет
3	    | failed	  | Транзакция не удалась               | Да
4	    | canceled	| Транзакция отменена                 | Да

## Таблица №3. Список услуг

Название              |	Описание
--------------------- | --------
wallet                | Пополнение кошелька алиф.моби
card                  | Пополнение карт Корти Милли, принадлежащие Алифу
card_all              | Пополнение карт Корти Милли
card_humouz           | Пополнение карт ХумоУз
card_uzcard           | Пополнение карт Узкарт
credit                | Пополнение кредита Алиф
deposit               | Пополнение депозита Алиф
invoice               | Оплата выставленных счетов
provider              | Пополнение услуг провайдеров
emv_qr                | Оплата по Emv Qr
invoice_qr            | Оплата по Qr
transfer_by_phone     | Перевод по номеру телефона в Таджикистан
transfer_by_phone_uz  | Перевод по номеру телефона в Узбекистан
card_visa_tj          | Пополнение карт Виза, принадлежащие Алифа
card_visa_foreign     | Пополнение карт Виза
