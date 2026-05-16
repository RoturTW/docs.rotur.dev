# Keys

> **Authentication:**\
> All routes use the **GET** method.\
> All required parameters must be passed as **URL query parameters** (after `?` in the URL).
>
>
>
> **The base url for this api is:**\
> https://api.rotur.dev/\
> \
> \
> **Manage your keys with the GUI:**\
> [https://rotur.dev/key-manager](https://rotur.dev/key-manager)

***

### GET `/keys/create`

**Description:**\
Create a new key, the data of a key is hidden unless the user owns it.

**Query Parameters:**

* `auth` — your rotur user token (required)
* `data` — data to associate with the key (required)
* `price` — price for the key (required)

**Example:**

```http
GET /keys/create?auth=YOUR_TOKEN&data=exampledata&price=10
```

***

### GET `/keys/get/<id>`

**Description:**\
Retrieve information about a specific key by ID.

**Path Parameter:**

* `<id>` — the ID of the key

**Query Parameters:**

* `auth` — your rotur user token (required)

**Example:**

```http
GET /keys/get/1234?auth=YOUR_TOKEN
```

***

### GET `/keys/mine`

**Description:**\
Retrieve an array of all keys you own.

**Query Parameters:**

* `auth` — your rotur user token (required)

**Example:**

```http
GET /keys/mine?auth=YOUR_TOKEN
```

***

### GET `/keys/check/<username>`

**Description:**\
Check if a user owns a specific key.

**Path Parameter:**

* `<username>` — the username to check

**Query Parameters:**

* `key` — the key to check against (required)

**Example:**

```http
GET /keys/check/misty?key=KEY_ID
```

***

### GET `/keys/revoke/<id>`

**Description:**\
Revoke a user's access to a key.

**Path Parameter:**

* `<id>` — the ID of the key

**Query Parameters:**

* `auth` — your rotur user token (required)
* `user` — the username to remove (required)

**Example:**

```http
GET /keys/revoke/1234?auth=YOUR_TOKEN&user=misty
```

***

### GET `/keys/delete/<id>`

**Description:**\
Delete a key by ID.

**Path Parameter:**

* `<id>` — the ID of the key

**Query Parameters:**

* `auth` — your rotur user token (required)

**Example:**

```http
GET /keys/delete/1234?auth=YOUR_TOKEN
```

***

### GET `/keys/update/<id>`

**Description:**\
Update the data associated with a key.

**Path Parameter:**

* `<id>` — the ID of the key

**Query Parameters:**

* `auth` — your rotur user token (required)
* `key` — the key to update (required)
* `data` — new data to set (optional)

**Example:**

```http
GET /keys/update/1234?auth=YOUR_TOKEN&key=price&data=5
```

***

### GET `/keys/admin_add/<id>`

**Description:**\
Manually add a user to a key (admin action).

**Path Parameter:**

* `<id>` — the ID of the key

**Query Parameters:**

* `auth` — your rotur user token (required)
* `key` — the key to add the user to (required)
* `username` — the username to add (required)

**Example:**

```http
GET /keys/admin_add/1234?auth=YOUR_TOKEN&key=KEY_ID&username=misty
```
