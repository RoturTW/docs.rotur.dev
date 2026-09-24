# tab\_info

Pages set `tab_info` to change the tab's title or icon. Set it to an object with `title` and `icon` keys, or to a string to change only the title.

{% tabs %}
{% tab title="OSL" %}
```javascript
tab_info = {"title":"my page","icon":"icn code"}
tab_info = "my page"
// current_tab_name works the same way
```
{% endtab %}
{% endtabs %}

`current_tab_name` is an older name for `tab_info`, and many `.web` pages still use it. If you are building a browser, support both.

## Implementations

{% tabs %}
{% tab title="Flufi Browser (OSL)" %}
```javascript
current_tab_name = null
tab_info = null

void page.update(data)

current_tab_name ??= tab_info
if current_tab_name != null and current_tab_name.type == "string" (
  page.title = current_tab_name
) else if current_tab_name.type == "object" (
  page.title = current_tab_name["title"] ?? page.title
  page.icon = current_tab_name["icon"] ?? page.icon
)
```
{% endtab %}
{% endtabs %}
