Let's start with installing the JWT strategy.

```shell
$ npm install passport-jwt
```

1. In `middleware/passport.js` require `JWTStrategy`.

```js
const JWTStrategy = require('passport-jwt').Strategy;
```

2. We will create a JWT strategy instance, which takes two arguments, an options object and a callback function.

```js
exports.jwtStrategy = new JWTStrategy({}, () => {});
```

The `options` object is an object literal containing options to control how the token is extracted from the request or verified.

3. Tokens can be extracted from the request in many ways. We will pass our token in the request's authorization header with the scheme bearer. We need to require this method.

```js
const { fromAuthHeaderAsBearerToken } = require('passport-jwt').ExtractJwt;
```

4. Now we will pass this method to our options object. Also, we will pass our secret key we defined in `config/keys.js`.

```js
exports.jwtStrategy = new JWTStrategy(
  {
    jwtFromRequest: fromAuthHeaderAsBearerToken(),
    secretOrKey: JWT_SECRET,
  },
  () => {}
);
```

5. Now the second argument, an asynchronous callback function, takes two arguments, the token's payload and `done` method. So the JWT strategy decodes the token and passes the payload as an argument.

```js
exports.jwtStrategy = new JWTStrategy(
  {
    jwtFromRequest: fromAuthHeaderAsBearerToken(),
    secretOrKey: JWT_SECRET,
  },
  async (jwtPayload, done) => {}
);
```

6. First we will check if the token is expired or not by comparing the expiration date to the date right now. If the token is expired, we will call `done` and pass it `null` and `false` as arguments, which will throw a `401` error.

```js
exports.jwtStrategy = new JWTStrategy(
  {
    jwtFromRequest: fromAuthHeaderAsBearerToken(),
    secretOrKey: JWT_SECRET,
  },
  async (jwtPayload, done) => {
    if (Date.now() > jwtPayload.exp) {
      return done(null, false); // this will throw a 401
    }
  }
);
```

7. If the token is not expired, we will find the user with the ID saved in the token. You can either use `findByPk` and pass it the `id` or `findOne` and pass it the `name`. Then we will pass the found user to `done`. If no user is found, it will throw a `401` error.

```js
async (jwtPayload, done) => {
  if (Date.now() > jwtPayload.exp) {
    return done(null, false); // this will throw a 401
  }
  try {
    const student = await Student.findById(jwtPayload.id);
    done(null, student); // if there is no user, this will throw a 401
  } catch (error) {
    done(error);
  }
};
```

8. Let's initialize our strategy in `app.js`. Require `jwtStrategy` and pass it to `passport.use()`.

```js
// Passport
app.use(passport.initialize());
passport.use(localStrategy);
passport.use(jwtStrategy);
```

Now how will we use this strategy? Basically we will call it before every request that needs permission before it's accessed.

9. Let's start with the enrollment route. In `courses` routes, require `passport` and in the `courseEnroll` call `passport.authenticate()` before the `courseEnroll` controller.

```js
router.post(
  '/:courseId/:studentId',
  passport.authenticate('jwt', { session: false }),
  courseEnroll
);
```

We will pass authenticate two arguments, `jwt` which tells passport to activate the `jwt` passport middleware which is the jwtStrategy method we created. That's its default name. Also we will pass it an object as a second argument, in this object set session to false. session is passport's default type of authentication, but we're not using it here.

10. And we will remove `:studentId` from here and use `req.user` instead.

```js
exports.courseEnroll = async (req, res, next) => {
  try {
    const studentId = req.user.id;
    await Course.findByIdAndUpdate(req.course.id, {
      $push: { students: studentId },
    });
    await Student.findByIdAndUpdate(studentId, {
      $push: { courses: req.course.id },
    });

    res.status(204).end();
  } catch (error) {
    next(error);
  }
};
```
