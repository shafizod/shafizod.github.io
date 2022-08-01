[![providers](https://img.shields.io/badge/doc-Providers-success)](https://shafizod.github.io/providers)
[![agents](https://img.shields.io/badge/doc-Agents-success)](https://shafizod.github.io/agents)
[![invoices](https://img.shields.io/badge/doc-Invoices-success)](https://shafizod.github.io/invoices)
[![webcheckout](https://img.shields.io/badge/doc-Webcheckout-success)](https://shafizod.github.io/webcheckout)

<div class="mytabs">

    <input type="radio" id="tabfree" name="mytabs" checked="checked">
    <label for="tabfree">Free</label>
    <div class="tab">
        <h2>Free</h2>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
    </div>

    <input type="radio" id="tabsilver" name="mytabs">
    <label for="tabsilver">Silver</label>
    <div class="tab">
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
        Адрес: https://alifpay.tj/gate/accounts
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
    </div>

    <input type="radio" id="tabgold" name="mytabs">
    <label for="tabgold">Gold</label>
    <div class="tab">
        <h2>Gold</h2>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.</p>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.</p>
    </div>

</div>
