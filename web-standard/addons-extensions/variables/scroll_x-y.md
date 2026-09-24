# scroll\_x / y

The current horizontal (`scroll_x`) and vertical (`scroll_y`) scroll position of the frame the page is drawn in. The browser sets them before each update.

{% tabs %}
{% tab title="OSL" %}
```javascript
goto scroll_x scroll_y
```
{% endtab %}
{% endtabs %}

## Implementations

{% tabs %}
{% tab title="Flufi Browser (OSL)" %}
```javascript
scroll_x = frame.scroll_h
scroll_y = frame.scroll
void page.update(data)
```
{% endtab %}
{% endtabs %}

