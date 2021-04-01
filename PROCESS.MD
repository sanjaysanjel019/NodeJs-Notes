 ### Step by Step process to develop a web Application using Node

Here will be our project structure:

- models
- controllers
- views
- assets
- routes
- config
- middlewares
- app.js
- server.js

Here **`app.js`**  will be the main entry point of the application where we will configure all the files and directories.
**`server.js`**  will be a sole file connecting to the database, making the server and handling the response to the database connection.


#### What will be is server.js ?
- Firstly, most of the dependencies will be required here.
 
- We use all the middlewares that we need in the program like body-parser, cors,morgan  etc. by app.use(bodyParser.json()), app.use(morgan('dev')) etc.
- We then make our Mongoose.Promise as Global Promise.
- We connect to the Database.
- Require the router in the main app.
- Encrypting the password using bcrypt

This step has to be taken down right before the password is  to be saved to the database. 

So,

```javascript
userSchema.pre("save", function(next){
if(!user.isModified('password') next();
bcrypt.hash(user.password,12,function(error,hashedPassword){
if(error) throw new Error('Password couldn;t be hashed)
user.password = hashedPassword;
})
```

The step above is just making a hashed password while the data is being stored in  database.

Now, if the user tries to login, the hasedPassword must be dehashed and be matched to the original password.
So,

```javascript
if(user)
bcrypt.compare(req.body.password, user.password, (err,matched)=>{
if(error) throw new Error('Password not matched')
if(matched)
-->SIGN THE ACCESSTOKEN
```

#### Implementing Auth based Tokenization

Using JWT Tokens, we have two steps:
	1. Sign
	2. Verify


Token is signed while the user logins, and the verify is implemented on the `AuthMiddleware.js.`

##### (AUTH ON AUTHENTICAION CONTROLLER)

```javascript
exports.auth = (req,res,next)=>{


const authorization = req.body.authorization;
if(req.path== "/api/login"){
next();
return;
}


if(!authorization) throw new Error("Unauthorized")


const accessToken = authorization.split(' ')[1];   //As the token comes as BearerToken fapfuhaifnfiaufhaiufhaioufaoinof we split and only take the 'fjaoiimofaj' part
const accessPayload = verify(accessToken, JWT_SECRET_KEY);  // In Verify we pass the accessToken and the secret key
if(!accessPayload){
throw new Error("Unauthorized")
}

if(accessPayload.type != 'accessToken'){
throw new Error("Unauthorized")
}

const user = await User.findById(accessPayload.userId);
if(!user){
throw new Error("User not found");
}



next();
 
}
```

Now when the user Logins, we sign the accessToken

The same steps follow for the tokenization, is that after the user initially logins,  we first decrypt his password and if he is a valid user, ,we then sign a accessToken.
so, here's the flow -->

1. If user exists
2. Compare the encrypted and hashed Password
3. if error, throw an error, else
4. if Matched  , generate an  accessToken

**The code**

```javascript
if(user)
{

bcrypt.compare(req.body.password., user.password, (err,matched) =>{
    if(err) throw new Error("User password not compared")
if(matched)
{
    accessToken = sign({userId:user.id, type:'accessToken'}, JWT_SECRET,{expiresIn:ACCESS_TOKEN_EXPIRYs})
}
}
}
```