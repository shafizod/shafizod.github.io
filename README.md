[![providers](https://img.shields.io/badge/doc-Providers-success)](https://shafizod.github.io/providers)
[![agents](https://img.shields.io/badge/doc-Agents-success)](https://shafizod.github.io/agents)
[![invoices](https://img.shields.io/badge/doc-Invoices-success)](https://shafizod.github.io/invoices)
[![webcheckout](https://img.shields.io/badge/doc-Webcheckout-success)](https://shafizod.github.io/webcheckout)

<details>
<summary>I could use some help...</summary>
<p>
{::options parse_block_html="false" /}

```c#
public class Order
{
    public int OrderId { get; set; }
    public int CustomerId { get; set; }

    public List<int> Products { get; set; }
}
```

{::options parse_block_html="true" /}
</p>
</details> 

<details>
  <summary>Check</summary>
    {::options parse_block_html="false" /}
  
    ```
    {
        "service": "card_all",
        "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
        "account": "5058270123456789",
        "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
        "amount": "10.00",
        "currency": "USD",
        "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
    }
    ```

    {::options parse_block_html="true" /}
</details>

<details>
<summary>Pay</summary>
    {::options parse_block_html="false" /}

    ```
    {
        "service": "card_all",
        "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
        "account": "5058270123456789",
        "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
        "amount": "10.00",
        "currency": "USD",
        "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
    }
    ```

    {::options parse_block_html="true" /}
</details>

<details>
<summary>PostCheck</summary>
    {::options parse_block_html="false" /}

    ```markdown
    {
        "service": "card_all",
        "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
        "account": "5058270123456789",
        "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
        "amount": "10.00",
        "currency": "USD",
        "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
    }
    ```

    {::options parse_block_html="true" /}
</details>

<ul class="tabs">

    <li class="tab">
        <input type="radio" name="tabs" id="tab1" checked="checked" />
        <label for="tab1">One</label>
        <div id="tab-content1" class="content">
            {::options parse_block_html="false" /}

            ```markdown
            {
                "service": "card_all",
                "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
                "account": "5058270123456789",
                "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
                "amount": "10.00",
                "currency": "USD",
                "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
            }
            ```

            {::options parse_block_html="true" /}
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab2" />
        <label for="tab2">Two</label>
        <div id="tab-content2" class="content">
            {::options parse_block_html="false" /}

            ```
            {
                "service": "card_all",
                "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
                "account": "5058270123456789",
                "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
                "amount": "10.00",
                "currency": "USD",
                "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
            }
            ```

            {::options parse_block_html="true" /}
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab3" />
        <label for="tab3">Three</label>
        <div id="tab-content3" class="content">
            {::options parse_block_html="false" /}

            ```{
                "service": "card_all",
                "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
                "account": "5058270123456789",
                "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
                "amount": "10.00",
                "currency": "USD",
                "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
            }
            ```

            {::options parse_block_html="true" /}
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab4" />
        <label for="tab4">Four</label>
        <div id="tab-content4" class="content">
            {::options parse_block_html="false" /}

            ```code
            {
                "service": "card_all",
                "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
                "account": "5058270123456789",
                "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
                "amount": "10.00",
                "currency": "USD",
                "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
            }
            ```

            {::options parse_block_html="true" /}
        </div>
    </li>

    <li class="tab">
        <input type="radio" name="tabs" id="tab5" />
        <label for="tab5">Five</label>
        <div id="tab-content5" class="content">
            {::options parse_block_html="false" /}

            ```json
            {
                "service": "card_all",
                "userid": "93e14e31-a8df-43c2-b8dc-9f6331447f04",
                "account": "5058270123456789",
                "txnid": "2AbyvPeSdnvKDJ48lBZZuBX4192",
                "amount": "10.00",
                "currency": "USD",
                "hash": "281829ecd0a082266a7571f885e1edea613c7e5a562626cda9203eaf1ee19d7a"
            }
            ```
            
            {::options parse_block_html="true" /}
        </div>
    </li>

</ul>