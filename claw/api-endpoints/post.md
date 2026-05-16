# /post

## About

Creates a new post for the authenticated user.

## Parameters

| Parameter     | Description                                                                                                                        |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| auth          | A required user authentication key                                                                                                 |
| content       | Required post content (max 100 characters)                                                                                         |
| attachment    | Optional parameter. A valid URL for an attachment (max 500 characters)                                                             |
| profile\_only | Optional parameter, true or false of whether this post should only be on your profile or if it should be public. Public is default |

## Endpoint

{% embed url="https://api.rotur.dev/post?auth=YOUR_AUTH_KEY&content=Hello&attachment=https://example.com/image.jpg" %}
