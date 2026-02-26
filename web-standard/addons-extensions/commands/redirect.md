# redirect

Redirects the current tab to a different url.

{% tabs %}
{% tab title="OSL / OWF" %}
```javascript
redirect "url"
```
{% endtab %}
{% endtabs %}

## Implementations

{% tabs %}
{% tab title="Flufi Browser (OSL)" %}
```javascript
def "redirect" "page" (
  if page.getType() == "string" (
    local url = Url.parse(page)
  ) else (
    local url = page
  )
  if url.scheme == "web" or url.scheme == "owtp" (
    url.scheme = "rtr"
  )
  UI.pages[UI.focused_page] = Document.new({
    url,
    type: url.file_name.split(".")[-1],
    isWeb: true
  })
  UI.utils.focusPage(UI.focused_page)
  local p @= UI.pages[UI.focused_page]
  UI.page_view.updatePage(p)
  UI.pages[UI.focused_page] @= p
)
```
{% endtab %}
{% endtabs %}
