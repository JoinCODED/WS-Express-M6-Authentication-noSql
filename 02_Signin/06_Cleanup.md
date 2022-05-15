Let's cleanup our code. Our `token`'s password shouldn't be thrown like this in the code.

1. Create a new folder called `config`.
2. In this folder, create a file called `keys.js`. This is where we will define our JWT's key and expiration time. The naming convention for configuration stuff is the following:

```js
module.exports = {
  JWT_SECRET: 'asupersecretkey',
  JWT_EXPIRATION_MS: 900000,
};
```

3. In `student` controller, require the `JWT_SECRET` and `JWT_EXPIRATION_MS`.

```js
const { JWT_SECRET, JWT_EXPIRATION_MS } = require('../config/keys');
```

4. Now replace `900000` with `JWT_EXPIRATION_MS`

```js
exp: Date.now() + parseInt(JWT_EXPIRATION_MS),
```

5. Also replace our secret key with `JWT_SECRET`.

```js
const token = jwt.sign(JSON.stringify(payload), JWT_SECRET);
```
