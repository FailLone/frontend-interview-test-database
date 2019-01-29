Here are the steps that happen internally for both calls:
(Hint: the only difference is in step 3)

```
    new Test():

    1. create new Object() obj
    2. set obj.__proto__ to Test.prototype
    3. return Test.call(obj) || obj;
    // normally obj is returned but constructors in JS can return a value
```

```
    Object.create( Test.prototype )

    1. create new Object() obj
    2. set obj.__proto__ to Test.prototype
    3. return obj;
```

So basically Object.create doesn't execute the constructor.