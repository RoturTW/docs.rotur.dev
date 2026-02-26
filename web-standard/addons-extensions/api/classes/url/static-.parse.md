# (static) .parse()

Parses a url string and turns it into an instanced URL class.

## Parameters

* String to parse

## Implementations

{% tabs %}
{% tab title="Flufi Browser (OSL)" %}
```scala
def parse(text) (
  local scheme = Config.url.defaults.scheme
  local name = Config.url.defaults.name
  local top = Config.url.defaults.top
  local sub = ""
  local path = null
  local filename = null
  
  local tokens = text.split("://")
  if tokens.len > 1 (
    scheme = tokens.shift()
    text = tokens.join("://")
  )
  
  if scheme == "local" (
    return Url.new(scheme,text,null,null,null,null,null)
  )
  
  tokens = text.split("/")
  text = tokens.shift()
  if tokens[-1].split(".").len > 1 (
    filename = tokens.pop()
  )
  path = tokens.join("/")
  
  tokens = text.split(".")
  if tokens.len == 1 (
    name = tokens[1]
  )
  if tokens.len == 1 (
    top = tokens[2]
    name = tokens[1]
  )
  if tokens.len >= 2 (
    top = tokens.pop()
    name = tokens.pop()
    sub = tokens.join(".")
  )
  
  return Url.new(scheme,name,top,sub,path,null,filename)
)
```
{% endtab %}
{% endtabs %}
