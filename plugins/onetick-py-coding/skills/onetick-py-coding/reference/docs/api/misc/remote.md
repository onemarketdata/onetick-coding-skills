# otp.remote

### ``remote(fun)``

This decorator is needed in case function `fun`
is used in ``apply()`` method in a Remote OTP with Ray context.

We want to get source code of the function locally
because we will not be able to get source code on the remote server.

##### SEE ALSO
`Remote OTP with Ray`.
