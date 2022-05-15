1. Back to our `signin` controller, let's console log `req.user`. You'll see the user's details passed from passport's local.

```js
exports.signin = (req, res) => {
  console.log('exports.signin -> req', req.user);
};
```

But what are we gonna do with the `user`? What does the frontend need as a proof that they're logged in? the proof that the user has access is called a `JWT` token.

A `JWT` token, which stands for `JSON Web Token`, is a standard for creating data with an optional signature. You can encrypt data into it, and that's what we will do. We will encrypt the user's data into it and pass it as a response to the frontend.

2. First we will decide what's the data that we want to send to the frontend, then save it in an object called `payload`. Let's start with adding the `student`'s ID and `name` for now.

```js
exports.signin = (req, res) => {
  const { user } = req;
  const payload = {
    id: user.id,
    name: user.name,
  };
};
```

The most important thing to pass to a token is the user's ID to know which user is signing in. Also, the expiry date of the token. When the user logs in, you have to decide how long are they allowed to be logged in.

3. So we will add another field to our payload called `exp`. We will pass it today's date and add to it how long we want our token to be available before it expires in milliseconds.

```js
const payload = {
  id: user.id,
  username: user.name,
  exp: Date.now() + 900000, // the token will expire 15 minutes from when it's generated
};
```

4. Before creating a token we will do some installations first. We will install the dependency `jsonwebtoken`.

```shell
$ npm install jsonwebtoken
```

5. In student controller , require `jwt` from `jsonwebtoken`.

```js
const jwt = require('jsonwebtoken');
```

6. Now we will generate our token! `jwt` has a method called `sign` that takes two arguments, the `data` we want to encrypt which is our `payload` -which HAS to be a string- and a `secret` code which is also a string.

```js
exports.signin = (req, res) => {
  [...]
  const token = jwt.sign(JSON.stringify(payload), "asupersecretkey");
};
```

7. Now we will pass this `token` as a response to our `signin` request.

```js
res.json({ token });
```

8. Let's make a request in `Postman`. Let's try to `login` in an existing user. Yes! We got a `token`! But how can we make sure that this token is correct?

9. We will use `jwt.io`, which is a website that generates and decrypts tokens. Copy paste your token into it, and check your information is correct.
