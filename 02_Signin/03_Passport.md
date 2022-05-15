BUT! We will not do the whole authentication in the `/signin` route, we will be using passport.

1. First we will install `passport`.

```shell
$ npm install passport
```

2. In `app.js`, we will require `passport` and add a middleware to initialize it. This middleware can be anywhere above `studentsRoutes`.

```js
const passport = require('passport');

// Passport Setup
app.use(passport.initialize());
```
