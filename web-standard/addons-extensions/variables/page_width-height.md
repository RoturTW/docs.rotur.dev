# page\_width / height

Pages set these variables to change the width and height of the frame they are drawn in.

{% tabs %}
{% tab title="OSL" %}
```javascript
page_width = 200
page_height = 1000
// page_len sets the height the same way
```
{% endtab %}
{% endtabs %}

`page_len` is an older name for `page_height`, and many `.web` pages still use it. If you are building a browser, support both.

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
