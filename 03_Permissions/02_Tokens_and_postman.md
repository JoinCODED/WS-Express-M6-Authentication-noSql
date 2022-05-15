When you want to send a request to an endpoint that requires you to be authenticated, you need to pass the token as your key for logging in.

1. Make a request to your `signin` endpoint. Copy the `token`.

2. Open a new tab, and set the URL and method of the endpoint that requires a token.

3. In `Postman` click on the `Authorization` tab.

4. Click on the `Type` dropdown menu, and choose `Bearer` Token.

5. Then on the right side, paste your `token`.

6. Send your request.
