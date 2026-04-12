

## JWT Token Creation (Summary)

Code:
const token = jwt.sign(
  { id: user._id, username: user.username },
  process.env.JWT_SECRET,
  { expiresIn: "1d" }
)

Explanation:

1. jwt.sign()
   → JWT (JSON Web Token) generate করার function।

2. Payload
   { id: user._id, username: user.username }
   → Token এর ভিতরে থাকা data।
   → সাধারণত user id বা username রাখা হয়।
   → Password বা sensitive data রাখা যায় না।

3. Secret Key
   process.env.JWT_SECRET
   → Token sign করার secret key।
   → সাধারণত .env file এ রাখা হয়।
   Example:
   JWT_SECRET=mysecretkey

4. Options
   { expiresIn: "1d" }
   → Token কতদিন valid থাকবে।
   Example:
   "10m" = 10 minutes
   "1h" = 1 hour
   "1d" = 1 day

5. Generated Token
   const token = jwt.sign(...)
   → এখানে token store করা হচ্ছে।

6. JWT Structure
   JWT = Header.Payload.Signature

7. Typical Flow
   User Login
   → Server password verify
   → JWT token generate
   → Frontend এ token পাঠানো
   → Frontend প্রতিটি request এ token পাঠায়
   → Backend token verify করে


=========================================================================


## res.cookie("token", token)

Meaning:
Server browser এ একটি cookie set করছে।

Syntax:
res.cookie(name, value, options)

Explanation:

1. name
"token"
→ cookie এর নাম

2. value
token
→ JWT token যেটা server generate করেছে

So browser এ store হবে:

token = <jwt_token>

Example:
token = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

Why two parameters?
কারণ cookie সবসময় name = value pair এ store হয়।

Example cookies:
username = rahim
theme = dark
token = jwt_token

Typical Login Flow:
User login
→ Server JWT token generate
→ res.cookie("token", token)
→ Browser cookie store করে
→ Next request এ cookie automatically server এ যায়


=====================================================================================

## Topic: Why user data is manually returned in response

Code:

const user = await userModel.create({
    username,
    email,
    password: hash
})

res.status(201).json({
    message: "User registered Successfully",
    user: {
        id: user._id,
        username: user.username,
        email: user.email
    }
})

Explanation:

1. userModel.create()
→ Database এ নতুন user save করে।
→ এটা পুরো user document return করে।

Example returned object from database:

{
 _id: "64abc123",
 username: "rahim",
 email: "rahim@gmail.com",
 password: "$2b$10$abcde...",
 __v: 0
}

2. Response এ আবার user object তৈরি করা হচ্ছে কারণ:

→ Client কে শুধু প্রয়োজনীয় data পাঠানো হয়
→ Sensitive data (password hash) পাঠানো হয় না
→ Database এর extra fields (__v etc.) পাঠানো হয় না

3. তাই manually safe object বানানো হয়েছে:

user: {
 id: user._id,
 username: user.username,
 email: user.email
}

4. Final response যা frontend পাবে:

{
 message: "User registered Successfully",
 user: {
   id: "64abc123",
   username: "rahim",
   email: "rahim@gmail.com"
 }
}

Summary:

Database → full user document save হয়  
Response → only safe fields পাঠানো হয়
(password বা unnecessary field বাদ দেওয়া হয়)