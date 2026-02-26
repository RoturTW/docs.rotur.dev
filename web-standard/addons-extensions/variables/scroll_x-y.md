# scroll\_x / y

Gives the page info on the current scroll of the containing frame

{% tabs %}
{% tab title="OSL" %}
```javascript
// example
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

