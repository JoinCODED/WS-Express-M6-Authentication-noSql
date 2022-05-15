1. Note that we can create many `students` with the same `name`! Every `student` should have a unique `name`. In our `Student` model, we will add a field called unique and give the property args the value true:

```js
const { model, Schema } = require('mongoose');

const StudentSchema = new Schema({
  name: {
    type: String,
    unique: true,
  },
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

2. Try again to create a `student`, isn't it weird that the hashed password is returned? Replace `newStudent` with a message that indicates a `student` is created.

```js
const newStudent = await Student.create(req.body);
res.status(201).json({ message: 'Student created successfully' });
```
