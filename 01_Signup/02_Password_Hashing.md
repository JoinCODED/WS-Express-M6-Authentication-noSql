1. To hash our passwords, we will use a library called `bcrypt`. Let's install it first.

```shell
$ npm install bcrypt
```

2. In the `student` controller, require `bcrypt`. To hash our password, we will use the method `bcrypt.hash` which takes two arguments, the `password` and `salt rounds`. Salting a password means adding random characters to a password before hashing it, the more rounds of salting you add the more secure your password is, but keep in mind that it will take a longer time.

```js
exports.signup = async (req, res) => {
  const { password } = req.body;
  const saltRounds = 10;

  try {
    const hashedPassword = await bcrypt.hash(password, saltRounds);
    const newStudent = await Student.create(req.body);
    console.log('exports.signup -> hashedPassword', hashedPassword);
    res.status(201).json(newStudent);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};
```

3. Let's test our method. Yaay, our password is hashed!
4. We can overwrite the password in the request's body and pass it to `Student.create()`.

```js
exports.signup = async (req, res) => {
  const { password } = req.body;
  const saltRounds = 10;

  try {
    const hashedPassword = await bcrypt.hash(password, saltRounds);
    req.body.password = hashedPassword;
    const newStudent = await Student.create(req.body);
    res.status(201).json(newStudent);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};
```
