We will add the functionality for the `student` to sign up and signin.

Fork and clone [this](https://github.com/JoinCODED/Demo-Express-M6-Authentication-noSql/) into your development folder.

1. Modify the `student` model, add a field for the password.

```js
const { model, Schema } = require('mongoose');

const StudentSchema = new Schema({
  name: String,
  courses: [
    {
      type: Schema.Types.ObjectId,
      ref: 'Course',
    },
  ],
  password: String,
});

module.exports = model('Student', StudentSchema);
```

2. Let's modify our `student` create route:

```js
router.post('/signup', studentsCreate);
```

3. And rename `studentsCreate` to `signup`.

```js
router.post('/signup', signup);
```

```js
exports.signup = async (req, res) => {
  try {
    const newStudent = await Student.create(req.body);
    res.status(201).json(newStudent);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};
```

4. Let's test it with Postman. Voila! It worked! But something looks wrong. As a user, how comfortable do you feel knowing that your password can be easily accessed by anyone who has access to the database? We need some kind of method to encrypt our password.
