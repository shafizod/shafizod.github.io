[![providers](https://img.shields.io/badge/doc-Providers-success)](https://shafizod.github.io/providers)
[![agents](https://img.shields.io/badge/doc-Agents-success)](https://shafizod.github.io/agents)
[![invoices](https://img.shields.io/badge/doc-Invoices-success)](https://shafizod.github.io/invoices)
[![webcheckout](https://img.shields.io/badge/doc-Webcheckout-success)](https://shafizod.github.io/webcheckout)

<details>
  <summary>Check</summary>

<div>
<div>

```markdown
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
```

</div>
</div>

</details>

<details>
  <summary>Pay</summary>

<div>
<div>

```markdown
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
```

</div>
</div>
</details>

<ul class="tabs">

    <li class="tab">
        <input type="radio" name="tabs" checked="checked" id="tab1" />
        <label for="tab1">One</label>
        <div id="tab-content1" class="content">
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab2" />
        <label for="tab2">Two</label>
        <div id="tab-content2" class="content">
            {{% md %}}
            ## Your content
            {{% /md %}}
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab3" />
        <label for="tab3">Three</label>
        <div id="tab-content3" class="content">
            <div>
                <div>
                    **markdown**
                </div>
            </div>
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab4" />
        <label for="tab4">Four</label>
        <div id="tab-content4" class="content">
<div>
<div>

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
```

</div>
</div>
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab5" />
        <label for="tab5">Five</label>
        <div id="tab-content5" class="content">
            <div>
                <div>
                    ```{
                    “service”: “card_all”,
                    “userid”: “93e14e31-a8df-43c2-b8dc-9f6331447f04”,
                    “account”: “5058270123456789”,
                    “txnid”: “2AbyvPeSdnvKDJ48lBZZuBX4192”,
                    “amount”: “10.00”,
                    “currency”: “USD”,
                    “hash”: “281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a”
                    }
                    ```
                </div>
            </div>
        </div>
    </li>

</ul>