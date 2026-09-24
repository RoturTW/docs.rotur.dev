# Page

A page, also called a document: one piece of content loaded in the browser.

```json5
{
    "url": <URL class instance>,
    "data": <object of custom variables>,
    "title": "Title of the page"
}
```

| Key | Type | Description |
| --- | --- | --- |
| `url` | [URL](../url/README.md) instance | The page's address |
| `data` | object | Custom variables the page stores |
| `title` | string | The page title. If it is not set, [`.getTitle()`](.gettitle.md) falls back to the URL. |

## Methods

| Method | Description |
| --- | --- |
| [`.new()`](static-.new.md) (static) | Creates a page |
| [`.getTitle()`](.gettitle.md) | Returns the page title |
