We will use a strategy or mechanism from `passport` called `LocalStrategy` for authenticating the user.

1. We will start with installing the strategy's dependencies:

```shell
$ npm install passport-local
```

2. Then in the `middleware` folder we will create a file called `passport.js` where we will configure our strategies.

3. In this file, we will require `LocalStrategy` and create a local strategy instance, which takes a callback function with three arguments `name`, `password` and `done`.

```js
const LocalStrategy = require('passport-local').Strategy;

exports.localStrategy = new LocalStrategy(async (name, password, done) => {});
```

We should specify which field is the username:

```js
exports.localStrategy = new LocalStrategy(
  { usernameField: 'name' },
  async (name, password, done) => {}
);
```

`done` is a `passport` function that attaches the `student` credentials to our request object. If `done` is passed one argument, it will pass it to an error handling middleware.

4. In this callback method, we will add a `try catch` statement and look for a `student` with the `name` sent in our database using `findOne` method. Don't forget to import `Student`.

```js
try {
  const student = await Student.findOne({
    name, // equivalent to { name : name }
  });
} catch (error) {
  done(error);
}
```

5. Next we will check if the `student` exists or not. If `student` exists, we will compare the password the user sent to the saved password to authenticate the user.

```js
try {
  const student = await Student.findOne({
    name, // equivalent to { name : name }
  });
  if (student) {
    // TODO: compare password to authenticate user
  }
} catch (error) {
  done(error);
}
```

6. But how can we compare the two passwords if our saved password is hashed? We will use a method from `bcrypt` called `compare`. Require `bcrypt` in `passport.js`.

```js
const bcrypt = require('bcrypt');
```

7. `compare` is a method that takes the two passwords and compares them, if they match it will return `true` else it will return `false`.

```js
try {
  const student = await Student.findOne({
    name, // equivalent to { name : name }
  });

  let passwordsMatch;

  if (student) {
    // TODO: compare password to authenticate user
    passwordsMatch = await bcrypt.compare(password, student.password);
  } else {
    passwordsMatch = false;
  }
} catch (error) {
  done(error);
}
```

8. The code has become so icky! Let's cleanup our condition! Replace the whole if condition with the following, lovely condition:

```js
const passwordsMatch = student
  ? await bcrypt.compare(password, student.password)
  : false;
```

9. Now, what shall we do? If the passwords match, we will pass the `student` to `done` which will pass it to our req object. But, `done`'s first argument is an error object. The second argument is what will be saved in the req object. So we will pass `null` as a first argument and `student` as the second argument.

```js
if (passwordsMatch) {
  return done(null, student);
}
```

10. But what if the passwords don't match? We will let the `signin` route take care of handling that. So we will pass `false` as a second argument to our req object.

```js
if (passwordsMatch) {
  return done(null, student);
}
return done(null, false);
```

11. Let's not forget to setup this method in `app.js`. Require `localStrategy` in `app.js`.

```js
// Passport Strategies
const { localStrategy } = require('./middleware/passport');
```

```js
app.use(passport.initialize());
passport.use(localStrategy);
```

To actually use this middleware we will use a `passport` method called `authenticate` that must be called before the `signin` controller.

13. In students routes, call `passport.authenticate` before the `signin` function. We will pass `authenticate` two arguments, `local` which tells passport to activate the local passport middleware which is the `localStrategy` method we created. That's its default name. Also we will pass it an object as a second argument, in this object set `session` to `false`. `session` is `passport`'s default type of authentication, but we're not using it here.

```js
const passport = require('passport');

router.post(
  '/signin',
  passport.authenticate('local', { session: false }),
  signin
);
```
