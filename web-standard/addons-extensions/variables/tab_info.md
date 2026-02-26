# tab\_info

Provides a way for pages to change the tab's title or icon.

{% tabs %}
{% tab title="OSL" %}
```javascript
tab_info = {"title":"my page","icon":"icn code"}
tab_info = "my page"
// the current_tab_name variable can also be used in the same way,
// if your making a browser, i recommend you also support current_tab_name
// as it is still used in a large amount of .web pages
```
{% endtab %}
{% endtabs %}

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
