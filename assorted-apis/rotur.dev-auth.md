# rotur.dev/auth

If you are making a website that connects to rotur, your users will often trust it more if it uses the official way to login to rotur.

## About

<https://rotur.dev/auth> is a versatile authentication API that supports both redirect-based and iframe-based authentication flows, allowing you to integrate Rotur authentication seamlessly into your application.

## Authentication Methods

### 1. Redirect-Based Authentication (Simple)

The traditional approach where you redirect your page to the auth endpoint and the user returns with a token.

You should redirect with https://rotur.dev/auth?return_to=url to make sure it goes back to your original page

#### How do I get a rotur token using this?

Have a look at the example page here:

{% embed url="https://rotur.dev/example%20auth" %}

You can view the source of this page here:

{% @github-files/github-code-block url="https://github.com/RoturTW/rotur.dev/blob/main/example%20auth.html" %}

### 2. Iframe-Based Authentication (Advanced)

For a more seamless user experience, you can embed the Rotur authentication flow directly in your application using an iframe and postMessage communication.

#### How iframe authentication works

1. **Create an iframe** that loads `https://rotur.dev/auth`
2. **Listen for postMessage events** from the iframe
3. **Handle the authentication result** when the user completes the login process and clicks "Allow Access"

#### Implementation Example

```html
<!DOCTYPE html>
<html>
<head>
    <title>Rotur Iframe Auth Example</title>
    <style>
        #auth-container {
            width: 400px;
            height: 500px;
            border: 1px solid #ccc;
            border-radius: 8px;
            margin: 20px auto;
        }
        #auth-iframe {
            width: 100%;
            height: 100%;
            border: none;
            border-radius: 8px;
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div id="auth-container">
        <iframe id="auth-iframe" src="https://rotur.dev/auth" style="display: none;"></iframe>
        <div id="loading">Loading authentication...</div>
        <div id="success" class="hidden">
            <h3>Authentication Successful!</h3>
            <p>Token: <span id="token-display"></span></p>
        </div>
    </div>

    <script>
        // Listen for messages from the iframe
        window.addEventListener('message', function(event) {
            // Verify the origin for security
            if (event.origin !== 'https://rotur.dev') {
                return;
            }

            // Handle authentication success
            if (event.data.type === 'rotur-auth-token') {
                // Authentication was successful
                const token = event.data.token;
                
                // Hide the iframe and show success message
                document.getElementById('auth-iframe').style.display = 'none';
                document.getElementById('success').classList.remove('hidden');
                document.getElementById('token-display').textContent = token;
                
                // You can now use the token for API calls
                console.log('Received Rotur token:', token);
                
                // Example: Store token for later use
                localStorage.setItem('rotur_token', token);
            }
        });

        // Show the iframe when page loads
        window.onload = function() {
            document.getElementById('auth-iframe').style.display = 'block';
            document.getElementById('loading').style.display = 'none';
        };
    </script>
</body>
</html>
```

#### PostMessage Events

The iframe will send the following message to the parent window when authentication is successful:

**Authentication Success:**

```javascript
{
    type: 'rotur-auth-token',
    token: 'user_auth_token_here'
}
```

Note: The message is sent to all origins (`'*'`), so always verify the origin in your event listener for security.

#### Security Considerations

1. **Origin Verification**: Always verify that messages come from `https://rotur.dev`
2. **HTTPS Only**: Only use iframe authentication over HTTPS connections
3. **Token Storage**: Store tokens securely (consider using secure HTTP-only cookies for sensitive applications)
4. **CSP Headers**: Ensure your Content Security Policy allows framing from `rotur.dev`
5. **Wildcard Origin**: The auth page sends messages to all origins (`'*'`), so proper origin verification is critical

#### Advantages of Iframe Authentication

- **Seamless UX**: Users don't leave your application
- **Responsive Design**: Easy to style and integrate with your UI
- **Real-time Feedback**: Immediate response without page refreshes
- **Mobile Friendly**: Works well on mobile devices without navigation disruption

#### CSS Integration Tips

```css
/* Style the iframe container */
.rotur-auth-container {
    max-width: 400px;
    margin: 0 auto;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
    overflow: hidden;
}

/* Responsive iframe */
.rotur-auth-iframe {
    width: 100%;
    height: 500px;
    border: none;
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
    .rotur-auth-container {
        box-shadow: 0 4px 6px rgba(255, 255, 255, 0.1);
    }
}
```
