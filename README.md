### Общие принципы протокола

Взаимодействие Агента (далее Партнер) и Системы (далее Алиф) построена на основе протокола HTTPS (RFC 2818: HTTP/1.1) в режиме "запрос-ответ", где инициатором запроса всегда является Партнер, а отвечающей стороной – Алиф.

Каждый платеж Партнера имеет уникальный идентификатор, который передается в каждом запросе. В Алифе, где хранятся все платежи всех Партнеров, у каждого платежа имеется уникальный идентификатор. По идентификатору Партнера производится дальнейшая сверка взаиморасчетов и решение спорных вопросов.

При обработке запроса от Партнера, Алиф выполняет требуемую операцию, а затем ответом передает Партнеру данные и результат выполнения операции.

## Организация протокола

Взаимодействие Партнера и Алиф происходит следующим образом:

1. Партнер формирует запрос в формате JSON, включая в него необходимые данные, и передает его по протоколу HTTPS на сервер Алифа по адресу https://alifpay.tj/gate/check для проверки счета клиента, а также создания платежа. Сервер Алифа осуществляет авторизацию клиента и разбор JSON. В случае успешной авторизации сервер Алифа выполняет необходимые действия и возвращает Партнеру ответ в формате JSON, в том числе результат выполнения запроса. Если Партнер получит успешный ответ ("code":200, "status":"accepted"), перейдет к следующему шагу (2).

2. После получения успешного ответа от запроса Check, необходимо подтвердить платеж отправив запрос по адресу https://alifpay.tj/gate/pay. Партнер перейдет к следующему шагу (3), если получит нефинальный ответ ("statusCode":2)

3. При полечении нефинального ответа от запроса Pay, необходимо отправить запрос Проверки статуса платежа по адресу https://alifpay.tj/gate/post_check с диапазоном 5 минут

4. Если Партнер повторит пункт (1) с теми же данными, получит такой ответ: "code":409, "status":"{статус платежа в Системе Алифа}". После получения такого ответа, нужно перейти к следующему шагу (2) только в том случае, если статус платежа в Системе Алифа равен "accepted".

5. Если Партнер повторит пункт (2) с теми же данными, получит такой ответ: "code":406, "status":"{статус платежа в Системе Алифа}". После получения такого ответа, если статус платежа не финальный (финальные статусы: success, failed, canceled), нужно перейти к шагу (3)

6. Кроме этих запросов есть еще необязательный запрос проверки счета получателя по адресу https://alifpay.tj/gate/accounts. Признаком успешности данного запроса является "code":200.

## Безопасность

1. Интерфейс должен принимать запросы с IP адресов Партнёра, которые были предварительно переданы техническим сотрудникам Алиф.

2. userid и password - ID и пароль пользователя Партнера. Генерируются и передаются приватно Партнеру со стороны Алиф. В рамках данной документации используются следующие данные: userid="476a1b42-b3dc-40e9-afad-4aaae1d640b9", password="cztef62wrwcysyubbbdnhlk1rs2cztfsqgwww7j0".

3. В запросах используется Аутентификационный токен (hash), который является по сути подписью запроса. Этот hash будет использоваться во всех запросах как часть процесса аутентификации и состоит из хэшированной строки в результате применения алгоритма HMAC-SHA256. Параметрами алгоритма являются Строка и пароль пользователя Партнера.

4. Срока для генерации hash состоит из конкатенации определенных полей из отличается в разных запросах. В обяснении каждого запроса приводится формула конкатенации этой Строки.

## Запросы check, pay, post_check

**Параметры запроса**

Параметр          | Тип           | Условие       | Описание
----------------- | --------------|---------------|----------------
service         	| string        | Обязательно   | Тип услуги. Смотрите в Таблице №3.
userid          	| string        | Обязательно   | ID пользователя Партнера.
hash            	| string        | Обязательно   | Подпись запроса. Генерируется по алгоритму SHA256 из конкатенации строк: userid+account+txnid+amount.fixed(2) посредством пароля пользователя Партнера.
account         	| string        | Обязательно   | Номера кредита/депозита/карты/кошелка/счёта клиента для пополнения.
amount          	| float         | Обязательно   | Сумма платежа в валюте Партнера либо услуги.
currency        	| string        | Обязательно   | Код валюты по стандарту ISO 4217, например TJS, RUB, USD, UZS, ...
txnid           	| string        | Обязательно   | Уникальный номер транзакции на стороне Партнера. В запросах check и pay одного платежа должен быть одинаковым.
phone           	| string        | Обязательно   | Номер телефона отправителя платежа. Этот номер используется для извещения плательщика или последующего контактирования.
fee             	| float         | Опционально   | Комиссия с клиента, которую взимает партнер.
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
----------------- | --------------|---------------|----------------
id                | int           | Условно       | Уникальный идентификатор платежа на стороне Алифа. Обязательно, если "status":"accepted".
datetime          | string        | Обязательно   | Дата и время обработки запроса в формате RFC3339Nano
code              | int           | Обязательно   | Числовой код ответа. Смотрите в Таблице №1
message           | string        | Обязательно   | Описание кода ответа.
status            | string        | Обязательно   | Описание статуса платежа.
statusCode        | int           | Обязательно   | Код статуса. Смотрите в Таблице №2.
amount            | string        | Обязательно   | Зачислямая сумма в валюте услуги.
fx                | string        | Обязательно   | Курс валюты.
topay             | object        | Условно       | Информация по кредитным линиям пользователя. Используется для вывода информации Пользователю и организации выбора Пользователем определенного кредита (транша) в процессе его оплаты. У каждого кредита (транша) есть свой номер и может быть от 01 до 999. Обязательно, если "status":"accepted", "service":"credit".
accountInfo       | string        | Опционально   | Информация о счета получателя.


```markdown


// Пример запроса №1
Метод: POST
Адрес: https://alifpay.tj/gate/{check | pay | post_check}
Accept: application/json
Content-type: application/json; charset=utf-8

{
  "service": "wallet",
  "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
  "hash": "6abd8da5482f9133bbc86c48d967f9ad771057efd91c80c8d89c7fb2c917bb6f",
  "account": "992928313003",
  "amount": 18000.00,
  "currency": "RUB",
  "txnid": "29sP8k9FKBR3obJAhzHOVX7o2Gc",
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

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b999292831300329sP8k9FKBR3obJAhzHOVX7o2Gc18000.00"

// Пример ответа

{
  "id": 14371303,
  "datetime": "2022-07-28T22:43:18.742486833+05:00",
  "code": 200,
  "message": "Ғ.Ш.",
  "status": "accepted",
  "statusCode": 0,
  "amount": "3022.20",
  "fx": "0.1679",
  "topay": null,
  "accountInfo": "{\"verified\":true}"
}


// Пример запроса №2
Метод: POST
Адрес: https://alifpay.tj/gate/{check | pay | post_check}
Accept: application/json
Content-type: application/json; charset=utf-8

{
  "service": "provider",
  "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
  "hash": "f5094d6ed0289c5dbe04d0bdc4d45fe2d753b78bffecbb6f022c5639c58f4d80",
  "account": "939145566",
  "amount": 15.05,
  "currency": "TJS",
  "txnid": "4ff79f2d-40ec-4f0a-aa18-4482b1d81bec",
  "phone": "+992935141010",
  "fee": 0.15,
  "providerId": 93,
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

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b99391455664ff79f2d-40ec-4f0a-aa18-4482b1d81bec15.05"

// Пример ответа

{
  "id": 14371677,
  "datetime": "2022-07-28T23:01:22.624092959+05:00",
  "code": 200,
  "message": "Аккаунт найден",
  "status": "accepted",
  "statusCode": 0,
  "amount": "15.05",
  "fx": "1",
  "topay": null,
  "accountInfo": "{}"
}


```




-------------------------------

## Запрос accounts

**Параметры запроса**

При получении запроса accounts Алиф проверит по указанной услуге наличие абонента с указанным идентификатором.

Параметр          | Тип           | Условие       | Описание
----------------- | --------------|---------------|----------------
service         	| string        | Обязательно   | Тип услуги. Смотрите в Таблице №3.
userid          	| string        | Обязательно   | ID пользователя Партнера.
hash            	| string        | Обязательно   | Подпись запроса. Генерируется по алгоритму SHA256 из конкатенации строк: userid+':'+dateTime посредством пароля пользователя Партнера.
account         	| string        | Обязательно   | Номера кредита/депозита/карты/кошелка/счёта клиента для пополнения.
amount          	| float         | Обязательно   | Сумма платежа в валюте Партнера либо услуги.
currency        	| string        | Обязательно   | Код валюты по стандарту ISO 4217, например TJS, RUB, USD, UZS, ...
providerId      	| int           | Условно       | Идентификатор услуги провайдера. Партнеру предоставляется список доступных услуг провайдеров. Обязательно, если "service":"provider".
datetime          | string        | Обязательно   | Дата и время запроса в формате RFC 7231

**Параметры ответа**

Параметр          | Тип           | Условие       | Описание
----------------- | --------------|---------------|----------------
code              | int           | Обязательно   | Числовой код ответа. Смотрите в Таблице №1
message           | string        | Обязательно   | Описание кода ответа.
amount            | string        | Обязательно   | Зачислямая сумма в валюте услуги.
fx                | string        | Обязательно   | Курс валюты.
topay             | object        | Условно       | Информацию по кредитным линиям пользователя. Обязательно, если "status":"accepted", "service":"credit".
accountInfo       | string        | Опционально   | Информация о счета получателя.


```markdown


// Пример запроса №1
Метод: POST
Адрес: https://alifpay.tj/gate/accounts
Accept: application/json
Content-type: application/json; charset=utf-8

{
  "service": "wallet",
  "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
  "hash": "e5a6f1344b3a15483d70e8d6b598b94ca08896a71f0acf9041648e12528e6009",
  "account": "992928313003",
  "amount": 18000.00,
  "currency": "RUB",
  "providerId": 0,
  "datetime": "Thu, 28 Jul 2022 23:01:22 +05"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9:Thu, 28 Jul 2022 23:01:22 +05"

// Пример ответа

{
  "code": 200,
  "message": "Ғ.Ш.",
  "accountInfo": "{\"verified\":true}",
  "topay": null,
  "amount": "3022.20",
  "fx": "0.1679"
}


// Пример запроса №2
Метод: POST
Адрес: https://alifpay.tj/gate/{check | pay | post_check}
Accept: application/json
Content-type: application/json; charset=utf-8

{
  "service": "provider",
  "userid": "476a1b42-b3dc-40e9-afad-4aaae1d640b9",
  "hash": "e5a6f1344b3a15483d70e8d6b598b94ca08896a71f0acf9041648e12528e6009",
  "account": "939145566",
  "amount": 15.05,
  "currency": "TJS",
  "providerId": 93,
  "datetime": "Thu, 28 Jul 2022 23:01:22 +05"
}

// Конкатенированная строка для генерации hash: "476a1b42-b3dc-40e9-afad-4aaae1d640b9:Thu, 28 Jul 2022 23:01:22 +05"

// Пример ответа

{
  "code": 200,
  "message": "Аккаунт найден",
  "accountInfo": "{}",
  "topay": null,
  "amount": "15.05",
  "fx": "1"
}


```

-------------------------------





ы**Таблица №1. Коды ответа**

Код |	Описание                                    | Фатальность | check | pay | post_check
--- | ------------------------------------------- | ----------- | ----- | --- | --------
200	|	Успешно                                     | Да          | +     | +   | +
285	|	Произошла ошибка при конвертации            | Да          | +     | +   | -
286	|	Курс валюты поменялся                       | Да          | -     | +   | -
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

**Таблица №2. Коды статуса**

Код   | Статус    | Описание                            |	Фатальность
----- | --------- | ----------------------------------- | ---------------------
0	    | accepted	| Транзакция принята, ожидание оплаты	| Нет
1	    | success	  | Транзакция прошла успешно           | Да
2	    | pending	  | Транзакция обрабатывается           | Нет
3	    | failed	  | Транзакция не удалась               | Да
4	    | canceled	| Транзакция отменена                 | Да

**Таблица №3. Типы услуг**

Название              |	Описание
--------------------- | ----------------------------------------------------------
wallet                | Пополнение услуги кошелька алиф.моби
card                  | Пополнение услуги карт Корти Милли Алифа
card_all              | Пополнение услуги карт Корти Милли
card_humouz           | Пополнение услуги карт ХумоУз
card_uzcard           | Пополнение услуги карт Узкарт
credit                | Пополнение услуги кредита Алиф
deposit               | Пополнение услуги депозита Алиф
invoice               | Пополнение выставленных счетов
provider              | Пополнение услуг провайдеров
emv_qr                | Оплата по Emv Qr
invoice_qr            | Оплата по Qr
transfer_by_phone     | Перевод по номеру телефона в Таджикистан
transfer_by_phone_uz  | Перевод по номеру телефона в Узбекистан
card_visa_tj          | Пополнение карт Виза Алифа
card_visa_foreign     | Пополнение карт Виза
