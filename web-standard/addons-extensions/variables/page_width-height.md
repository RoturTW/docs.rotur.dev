# page\_width / height

Lets pages change their containing frame's height and width.

{% tabs %}
{% tab title="OSL" %}
```javascript
page_width = 200
page_height = 1000
// the page_len variable can also be used in the same way,
// if your making a browser, i recommend you also support page_len
// as it is still used in a large amount of .web pages
```
{% endtab %}
{% endtabs %}

## Implementations

{% tabs %}
{% tab title="Flufi Browser (OSL)" %}
```javascript
page_width = 0
page_height = 0
page_len = 0

void page.update(data)

page.width = page_width
page.height = page_len ?? page_height
```
{% endtab %}
{% endtabs %}
