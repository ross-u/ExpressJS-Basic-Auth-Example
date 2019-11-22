# Node.js | Basic Authorization



<br>



## Authorization

### What is the authorization?

- Authorizing a user means giving access to certain routes / resources only to the users that are authorized.
- Authorization is the process of setting infrastructure that authorizes user access to specific resources.



<br>

(On the other hand **authentication** describes the process of authenticating the user, e.g. via Login form, sessions, tokens );



<br>



##  Types of authorization

We have several ways to provide authorization in our applications.



#### Email and password

The most basic approach for authorization:. 

**PROS**: Everyone has an email account 100% of users covered.





#### Username

Some applications like Twitter authorize their users with a `username`.  

**PROS**: You can show the username to other users in the application, providing them some privacy. 





#### Social login

With the rise of the social networks, a new way to authorize users appears: it’s called `social login`. 

**PROS**: It’s easy and fast to implement and use, for the developers and the web users.



<br>



#### A common factor in every authorization types is the password. 



<br>



## Password Security & Encryption

We can send a form submission data in two different ways, depending on the HTTP method :



- **GET**:  we submit the form values through the URL query string.

- **POST**: we submit the form values hidden in the request body.



<br>



**We have to encrypt the password.**



Doesn't matter if we use POST / GET method we can't leave the password as a plaint text in the database or out of it, as this would be a security risk.



<br>



### Hash functions

- To encrypt our passwords, we are going to use a [Hash function](https://en.wikipedia.org/wiki/Cryptographic_hash_function).

- A [hash function](https://en.wikipedia.org/wiki/Hash_function) is a function that, given any string as a parameter, generates a unique string of data.

- Hash functions are used in cryptography to ensure the integrity of the text. The same string will have always have the same unique hash.



<be>



It’s not impossible to **decrypt a hash value to get its original content**.

Decrypting a hash value **is just extremely impractical**. 

Because of the math involved and due to computationally intense operations.

Even if the malicious user steals our encrypted passwords, It could take from months to years for it to be decrypted.



There are a lot of algorithms used to encrypt data with hash functions. The most common ones are MD5, SHA, SHA-1, BLAKE… and bcrypt. 

We are going to use bcrypt to hash our passwords.





<br>



### Bcrypt



[Bcrypt](https://www.npmjs.com/package/bcrypt) is a hashing algorithm thats meant to transform a plain text password into something that's undecipherable.



<https://www.npmjs.com/package/bcrypt>



Hashing scrambles our password into a long string of characters that is practically impossible to decode via a mathematical method, 



<br>



**Salt** is [random](https://en.wikipedia.org/wiki/Random_Number_Generator) data that is used as an additional input to a [one-way function](https://en.wikipedia.org/wiki/One-way_function) that "[hashes](https://en.wikipedia.org/wiki/Cryptographic_hash_function)" [data](https://en.wikipedia.org/wiki/Data_(computing)), a [password](https://en.wikipedia.org/wiki/Password) or [passphrase](https://en.wikipedia.org/wiki/Passphrase). 



<br>



#### Encryption with bcrypt

Node.JS [bcrypt package](https://www.npmjs.com/package/bcrypt) allows us to encrypt passwords using the bcrypt algorightm. 



### [OPEN IMAGE](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_83d7129f38bde763a2557d189ab98a7b.png)

![img](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_83d7129f38bde763a2557d189ab98a7b.png)





<br>

## Sign up workflow



### [OPEN IMAGE](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_7776ce6ec2afe91c8b15fec37ba1e24a.png)

![img](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_7776ce6ec2afe91c8b15fec37ba1e24a.png)





<br>

## Demo / Code Along 



<br>



### Create the project using express generator

```bash
express --view=hbs basic-auth

cd basic-auth
```





### Install dependencies and create additional folders and files

```bash
# Install all the dependencies listed in package.json by express generator
npm i


# Install mongoose and bcrypt
npm i --save mongoose bcrypt


# Install nodemone
npm i --save-dev nodemon


# Create additional folders and files
mkdir views/auth-views models
touch views/auth-views/signup.hbs models/User.js routes/auth.js


# Remove routes/users
rm routes/users.js


code .

```







<br>





## Our file structure



```js
📦basic-auth
 ┣ 📂bin
 ┣ 📂node_modules
 ┃
 ┣ 📂models
 ┃ ┗ 📜User.js
 ┃
 ┣ 📂public
 ┃
 ┣ 📂routes
 ┃ ┣ 📜auth.js
 ┃ ┗ 📜index.js
 ┃
 ┣ 📂views
 ┃ ┣ 📂auth-views
 ┃ ┃ ┗ 📜signup.hbs
 ┃ ┃
 ┃ ┣ 📜error.hbs
 ┃ ┣ 📜index.hbs
 ┃ ┗ 📜layout.hbs
 ┃
 ┣ 📜app.js
 ┗ 📜package.json
```



<br>



## Link the `routes/index.js` file and `routes/auth.js` 

​	

##### `app.js`

```js
// app.js


// var indexRouter = require('./routes/index');    <-- REMOVE
// var usersRouter = require('./routes/users');    <-- REMOVE


const router = require('./routes/index');		//		<-- ADD


// 	...
// 	...


// app.use('/', indexRouter);				// REMOVE
// app.use('/users', usersRouter);	// REMOVE

// Routes
app.use('/', router);								//		<-- ADD
```



<br>



#### Link the `authRouter` in the `routes/index.js`

##### `routes/index.js`

```js
// routes/index.js

const express = require("express");
const router = express.Router();

const authRouter = require('./auth');         // ADD

// *	'/signup'
router.use('/signup', authRouter);            // ADD


// GET '/'
router.get('/', (req, res, next) => {
  res.render('index', {title: 'Basic auth code along'});
});

module.exports = router;
```





<br>



#### Create a GET `/signup` route in the  `authRouter` 



##### `routes/auth.js`

```js
// routes/auth.js
const express = require("express");
const router = express.Router();
const User = require('./../models/User');


//	GET '/signup'
router.get('/', (req, res, next) => {
  res.render("auth-views/signup");
});

module.exports = router;
```



<br>



## Create mongoose connection &  `User` model





#### Create `mongoose` connection.



##### `app.js`

```js
const mongoose = require("mongoose");

// ...
// ...

mongoose.connect('mongodb://localhost:27017/basic-auth', {
  useNewUrlParser: true,
  reconnectTries: Number.MAX_VALUE,
  useUnifiedTopology: true
});

```



<br>







### Create `User` model



##### `models/User.js`

```js
// models/User.js

const mongoose = require("mongoose");
const Schema   = mongoose.Schema;

// CREATE THE SCHEMA
const userSchema = new Schema({
  username: String,
  password: String
}, {
  timestamps: true
});

// CREATE THE MODEL
const User = mongoose.model("User", userSchema);

// EXPORT THE MODEL
module.exports = User;
```





<br>



### Create a Sign Up form	-	 `views/auth/signup.hbs`



##### `views/auth-views/signup.hbs`

```html
<!--  views/auth-views/signup.hbs   -->


	<!--  CREATE THE 'POST' FORM   -->
	<form id="form" action="/auth/signup" method="POST">
    <label for="username">Username</label><br>
    <input type="text" name="username"
      placeholder="Your username">

    <label for="password">Password</label><br>
    <input type="password" name="password"
      placeholder="********">

    <button type="submit">Create account</button>
  </form>


{{!-- error message display --}} 
{{#if errorMessage}}
  <div class="error-message">{{ errorMessage }}</div>
{{/if}}

```





<br>



### Add styles to the form



##### `public/stylesheet/style.css`

```css
/*   public/stylesheet/style.css   */

#form {
  margin: 30px auto;
  width: 380px;
}

#form label {
  color: #666;
  display: inline-block;
  margin-bottom: 10px;
  text-align: center;
  width: 100%;
}

#form input,
#form button {
  box-sizing: border-box;
  font-size: 14px;
  outline: 0;
  padding: 4px;
  width: 100%;
  margin-bottom: 20px;
}

button {
  background: #43a3e6;
  border: 1px solid #43a3e6;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
  display: inline-block;
  font-size: 14px;
  padding: 8px 16px;
  text-decoration: none;
  text-transform: uppercase;
  transition: .3s ease background;
  width: 100%;
}

button:hover {
  background: #fff;
  color: #43a3e6;
  transition: .3s ease background;
}

.error-message {
  background: #F02B63;
  box-sizing: border-box;
  color: #fff;
  margin: 20px auto;
  padding: 20px;
  width: 100%;
}

```



<br>



### Update home page `views/index.hbs`



##### `views/index.hbs`

```html
<!--   views/index.hbs   -->


<!--   ...   -->

<!--   ADD A SIGN UP BUTTON   -->
<a href="/signup">
  <button> SIGN UP </button>
</a>

```





<br>



### Update`package.json`



##### `package.json`

```js
"scripts": {
	"start-dev": "nodemon ./bin/www",

```



<br>



### Run the server 

```bash
npm run start-dev
```



<br>



#### Visit [`localhost:3000/`](http://127.0.0.1:3000/) to see the homepage.

#### Visit [`localhost:3000/signup`](http://127.0.0.1:3000/signup) to see the form.



<br>





### Create the POST route for handling the form data submission.



The below are the steps our sign up process will follow.



##### `routes/auth.js`

```js
// routes/auth.js

// 1 - Require `bcrypt` for passwords hashing
// 2 - Create variable for the number of salt rounds


// POST '/auth/signup'
router.post("/signup", (req, res, next) => {
  
  // 3 - Deconstruct the `username` and `password` from req.body
  

  // 4 - Check if `username` or `password` are empty and display error message
  
  
  // 5 - Check the users collection if the username already exists 
  
			// > if `username` already exists in the DB and display error message
  
      // > If `username` doesn't exist generate salts and hash the password 
      
  		// > After hashing the password, create new user in DB
  	
  				// >  When the new user is created, redirect to '/home' page
  
  
  	// catch errors from User.findOne
});

```



<br>



### Now implement the `auth/signup` route. 



##### `routes/auth.js`

```js
// Require `bcrypt` to hash passwords and specify number for salt rounds
const bcrypt = require('bcrypt');
const saltRounds = 10;

// POST '/auth/signup'
router.post("/signup", (req, res, next) => {
  
  // Deconstruct the `username` and `password` from request body
  const { username, password } = req.body;

  
  // Check if `username` and `password` are empty and display error message
  if (username === '' || password === '') {
    res.render('auth-views/signup', 
               { errorMessage: 'Enter the username and password.'}
              );
    return;
  }
  
  
  	// Check the users collection to check the username
    User.findOne({ username })
      .then((user) => {
      
			// > if `username` already exists in the DB display error message
      if ( user !== null ) {
        res.render('auth-views/signup',{ errorMessage:'The username already exists'});
        return;
      }
      
      	// > If `username` doesn't exist generate salts and hash the password 
      const salt = bcrypt.genSaltSync(saltRounds);
      const hashedPassword = bcrypt.hashSync(password, salt);
      
      
  		// > After hashing the password, create new user in DB and redirect to home 
      User.create( { username, password: hashedPassword})
      	.then(() => res.redirect('/'))
        .catch((err) => res.render('auth-views/signup',{ errorMessage:'Error while trying to create a  new user'}));
      
     })
     .catch((err) => next(err));	// catch errors from User.findOne
  
});

```





<br>



### Additional security - Password strength measurement

- To implement the password strength check functionality, we can use the package [`zxcvbn`](https://www.npmjs.com/package/zxcvbn). 

- `zxcvbn` package is a tool to check the password strength in both front-end and back-end.



<br>



#### Install the `zxcvbn` package

```bash
npm i --save zxcvbn
```





##### `routes/auth.js`

```js
//  routes/auth.js

//	...
//			...

// Import zxcvbn
const zxcvbn = require('zxcvbn');

/* Create the check for the password strength score after:

  	if (username === '' || password === '') {}

*/
			// Check the password strength
			if (zxcvbn(password).score < 3) {
    		res.render('auth/signup',
      		{ errorMessage: 'Password too weak, try again' }
    		);
    		return;
  		}


```





<br>



## Basic Authentication & Sessions



<br>



#### When a user logs into our web app, authentication allows us to know which user (of all users in our system) is visiting the page.

####  

#### -  Authorization is related to signing up and the overall process of allowing users to access certain resources.

#### -  Authentication is related to authenticating the user during the current request, via login, sessions or tokens.





<br>



## Session

##### Because HTTP is stateless, in order to associate a request to any other request, you need a way to store user data between HTTP requests.



##### Sessions are an industry standard.



##### Session allows servers to store user-specific data during multiple request/response interactions between a client and server itself.





- We will use the [`express-session`](https://www.npmjs.com/package/express-session) package to generate sessions in Express. 

- We use it in combination with [`connect-mongo`](https://www.npmjs.com/package/connect-mongo) to store session data inside our Mongo database.





<br>





##### - When using the `express-session`  session data is by default stored in the memory if other 3rd party db is not specified otherwise (not persistent). 



##### - The best choice is a memory cache like Redis, for which you need to setup its own infrastructure.





<br>





## Cookies

Cookies are small files which are stored on a user’s computer. They are designed to hold a small amount of specific data from a website and can be accessed either by the web server or the client computer.



Cookies are a convenient way to carry information between sessions, without having to burden a server machine with massive amounts of data storage.



 **We can inspect cookies information with the [Chrome Dev Tools](https://developer.chrome.com/devtools)  in the `Application` tab:**





![](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_a395a41b136de0d919e1a2bb0109fc5b.png)







<br>





## Log in

##### Authentication is the process where users provide their credentials (username/email and password) and send them to the server.

##### The user is authenticated if the username and password are correct. 

##### Once the user has been authenticated, we have to create a session to keep the user logged in.  In this session we can store all the information we need.







#### Login diagram

![img](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_5308102df9341ab5a67d20d61bc61b15.png)





<br>







## Code Along 

### We will continue working on the code from the previous project





#### Fork and Clone the Previous repo -> [Node-Basic-Authorization](<https://github.com/ross-u/Node---Basic-Authorization---Code-Along-Done>)





<br>



#### Install the dependencies

```bash
npm i
```



<br>



In the same directory install `express-session` and `connect-mongo`

```bash
npm install --save express-session connect-mongo
```





### Require the `express-session`  and `connect-mongo` packages (before the routes)



##### `app.js`

```js
const session    = require("express-session");
const MongoStore = require("connect-mongo")(session);

...
...



// Session middleware
app.use(session({
  secret: "basic-auth-secret",
  // cookie: { maxAge: 3600000 * 1 },	// 1 hour
  resave: true,
  saveUninitialized: false,
  store: new MongoStore({
    mongooseConnection: mongoose.connection,
    ttl: 60 * 60 * 24 * 7 // Time to live - 7 days (14 days - Default)
  })
}));

//		BEFORE THE ROUTES

// Routes
app.use('/', router);
```



- **secret**: Used to sign the session ID cookie (*required*)

- **cookie**: Object for the session ID cookie. In this case, we only set the `maxAge` attribute, which configures the expiration date of the cookie (in milliseconds).

- **store**: Sets the session store instance. In this case, we create a new instance of `connect-mongo`, so we can store the session information in our Mongo database.

  



<br>



### Create a login form -  `views/auth/login.hbs`



##### `views/auth/login.hbs`

```html
<!--   views/auth/login.hbs    -->


<form action="/login" method="POST" id="form">
  <h2>Login</h2>

  <label for="username">Username</label>
  <input type="text" name="username">

  <label for="password">Password</label>
  <input type="password" name="password">

  {{#if errorMessage }}
    <div class="error-message">{{ errorMessage }}</div>
  {{/if}}

  <button type="submit">Login</button>

  <p class="account-message">
    Don't have an account? <a href="/signup">Sign up</a>
  </p>
</form>
```



<br>



### Create a login router - `routes/login.js` 



##### `routes/login.js`

```js
const express = require('express');
const router = express.Router();
const User = require('./../models/User');

const bcrypt = require('bcrypt');


//  GET   '/login'
router.get('/', (req, res, next) => {
  res.render("auth/login");
});



module.exports = router;
```





<br>



### Update `routes/index.js` and include `loginRouter` 

##### `routes/index.js`

```js
// routes/index.js

...
...
const loginRouter = require('./login');

// * '/login' 
router.use('/login', loginRouter);
```





<br>



#### Start the server

```bash
npm run start-dev
```



<br>



### Visit [`localhost:3000/login`](http://127.0.0.1:3000/login) to see the form





<br>





### Create the `/login` POST route to check the user credentials 



##### `routes/login.js`

```js
// routes/login.js

...
	...


//  POST   '/login'
router.post("/", (req, res, next) => {
  
  const theUsername = req.body.username;
  const thePassword = req.body.password;

  if (theUsername === "" || thePassword === "") {
    res.render("auth/login", {errorMessage: 'Indicate username and password.'});
    return;
  }

  User.findOne({ "username": theUsername })
  .then(user => {
      if (!user) {
        res.render("auth/login", { errorMessage: 'The username doesn\'t exist.' });
        return;
      }
    
    	const passwordCorrect = bcrypt.compareSync(thePassword, user.password);
      
    	if (passwordCorrect) {  
        
        // Save the login in the session!
        // req.session is used to store values for our use
        req.session.currentUser = user;
        res.redirect("/");
      } 
    	else res.render("auth/login", { errorMessage: 'Incorrect password' });
  })
  .catch(error => next(error));
  
});

module.exports = router;
```





<br>





### Update `index.hbs`

```html
<h1>Home</h1>
<p>Welcome</p>

<div id="container">
  <a href="/signup">
    <button>Sign up</button>
  </a>

  <a href="/login">
    <button>Login</button>
  </a>
  
  <a href="/secret">
    <button>Protected page (requires authentication!)</button>
  </a>
</div>

```





<br>



## Authenticating with the session





##### We will use `session.currentUser`  to check if the user is logged in to access the other routes in the app.



We check the `session.currentUser` on each incoming request to see if the user who is making it is authenticated already.







## Create the authentication middleware

In order to let the user access a certain resource/route once he has a valid cookie that has the id of the last login session, we need to create a middleware that checks if user has a valid cookie.

If the cookie coming with the request from the browser actually matches an existing session in the DB, `express-session` will create the object `req.session.currentUser`.

So, the presence of this object (`req.session.currentUser`) indicates that the user's request came with a valid cookie and that he is therefore authenticated.



### Create `routes/site-routes.js` , and use it to check if user is authenticated



##### `routes/site-routes.js`

```js
// routes/site-routes.js

const express = require("express");
const router = express.Router();

router.use((req, res, next) => {
  if (req.session.currentUser) { // <== if there's user in the session (user is logged in)
    next(); // ==> go to the next route ---
  } 																//		|
  else {                          	//    |
  	res.redirect("/login");       	//    |
  }                                 //    |
});																	//		|
// 		 ------------------------------------  
//     | 
//     V
router.get("/secret", (req, res, next) => {
  res.render("secret");
});

module.exports = router;
```







### OR Using the middleware helper function

##### `routes/site-routes.js`

```js
// routes/site-routes.js

const isLoggedIn = (req, res, next) => {
  if (req.session.currentUser) {
    console.log('GOOD TO GO');
    next();
  } else {
    res.redirect('/login');
  }
}
 
//  ...
//  ...
  
router.get('/secret', isLoggedIn, (req, res, next) => {		//    <--- UPDATE
  res.render('secret');
});
```

<br>





### Update `app.js` and the order of the routes



##### `app.js`

```js
//  ...

const siteRoutes = require('./routes/site-routes');


// Routes
app.use('/', router);
app.use('/', siteRoutes);	// This route verifies the user's session

```







<br>





### Create a view for the protected route `views/secret.hbs`



##### `views/secret.hbs`

```html
<div id="container">
  <h2>You are in a session!</h2>
</div>
```





<br>





## Logout



The logout is the process where the user “closes” the connection between them and the web app.

It’s the opposite of the login.

Sessions package has a `destroy()` method which, when executed, will remove the user’s session.

Calling `req.session.destroy()` will cause the session to be deleted from the MongoDB session storage.



<br>

### Create the `/logout` route.



##### `routes/logout.js`

```js
const express = require('express');
const router = express.Router();


//  GET '/logout'
router.get('/', (req, res, next) => {
  req.session.destroy((err) => {
    // can't access session here
    res.redirect("/login");
  });
});


module.exports = router;
```





<br>



#### Update `routes/index.js`



##### `routes/index.js`

```js
// routes/index.js

//	...

const logoutRouter = require('./logout');


// * '/logout' 
router.use('/logout', logoutRouter);
```





<br>





### Add logout button to `views/index.hbs`



##### `views/index.hbs`

```html
<a href="/logout">
	<button>Logout</button>
</a>
```





<br>



## Important

Cookies are sent by the browser in every request as long as the URL requested is within the same domain and path defined in the cookie (and all of the other restrictions -- secure, httponly, not expired, etc) hold, then the cookie will be sent for every request.


<br>

## Diagram

![](https://github.com/ross-u/ExpressJS-Basic-Auth-Example/blob/master/BE%20API%202.jpg)


