Let's start with creating our `/signin` route.

1. In the `student` controller, create a `signin` controller function. For now, let's just console log the req object.

```js
exports.signin = (req, res) => {
  console.log('exports.signin -> req', req);
};
```

2. In `student` routes, import the `signin` controller function, and create a new route for `/signin`.

```js
router.post('/signin', signin);
```
