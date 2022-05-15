Shouldn't the user be automatically logged in after creating a new account? How can we do that? We can basically pass the token as a response.

1. In student controller's `signup` function, create a `payload` object that takes its details from newStudent and encrypt it.

```js
const newStudent = await Student.create(req.body);
const payload = {
  id: newStudent.id,
  name: newStudent.name,
  exp: Date.now() + JWT_EXPIRATION_MS,
};
const token = jwt.sign(JSON.stringify(payload), JWT_SECRET);
```

2. Pass the `token` as a reponse.

```js
res.status(201).json({ token });
```

3. Test it in Postman. Yes! We received a token!
