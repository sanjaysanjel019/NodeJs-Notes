### Global Error Handling Nodejs

We handle the errors in our application globally using a Error middleware.

The error middle ware takes four arguments - `error`,`req`,`res` and `next` in that same order.

```javascript
app.use((err,req,res,next)=>{
err.statusCode = err.statusCode || 500;
err.status = err.status || 'error';

res.status(err.statusCode).json({
    status:err.status,
    message:err.message
})
})
```

So, as we see, we've used a global error handling middleware in our app.js file to handle the errors.

#### Working Mechanism

In our code where we get the error, we make new Errors and then throw them so that we can handle them effectively.
That's why we must use good error status code while writing our program.

**Note:** *If we give any kind of argument on `next()` function, Express automatically knows that it should be an error handler.*

#### Handling Errors in a Classy Way
So,we create a class where we define the constructor to take the error statusCode and status and then handle the error with the use of that class.

We created this class is utils folder of our application.

```javascript
class AppError extends Error {
    constructor(message, statusCode) {
        super(message);


        this.statusCode = statusCode;
        this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
        this.isOperational = true;


        Error.captureStackTrace(this, this.constructor);
    }
}

module.exports = AppError;
```


Then we also created a Error Controller in our controller directive on our application and placed the following code:

```javascript
module.exports = (err, req, res, next) => {
    err.statusCode = err.statusCode || 500;
    err.status = err.status || 'error';


    res.status(err.statusCode).json({
        status: err.status,
        message: err.message
    });
};
```

So, where ever we need to handle the error, we import an pass this function as a parameter to the `app.use()` middleware.


