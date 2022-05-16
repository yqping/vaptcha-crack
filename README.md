### How VAPTCHA standard version realizes multi-domain association

#### Description

VAPTCHA is a highly secure smart CAPTCHA with no limit on the amount of verification, but the standard version(SV) can only be bound one domain name to each VAPTCHA unit. The following is a way to bypass the restriction. It has been configured and can be used directly. The demo is the invisible mode, check the [official docs](https://en.vaptcha.com/document/install.html#Web%20side%20deployment) for other modes.

#### Front end deployment

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>DEMO - Invisible mode</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.8.0/jquery.min.js"></script>
    <script src="https://v-cn.vaptcha.com/v3.js"></script>
</head>

<body>
    <button id="login-button">Invisible mode</button>

    <script>
        vaptcha({
            vid: '621e362fe82e6539de8fa7cf',
            mode: 'invisible',
            scene: 0,
            area: 'auto',
        }).then(function (VAPTCHAObj) {
            // Save the VAPTCHA instance to a local variable
            obj = VAPTCHAObj;

            // Verification succeeded, Continue
            VAPTCHAObj.listen('pass', function () {
                serverToken = VAPTCHAObj.getServerToken();
                var data = {
                    server: serverToken.server,
                    token: serverToken.token,
                }

                // Click login button to submit data to server, The following pseudo code is for reference only.
                $.post('/login', data, function (r) {
                    if (r.code == 200) {
                        console.log('Login successful')
                    } else {
                        console.log('Login failed')

                        // Wrong password, reset VAPTCHA.
                        VAPTCHAObj.reset()
                    }
                })
            })

            // After the instance initialization is completed, the user clicks the login button to perform VAPTCHA.
            $('#login-button').on('click', function () {
                obj.validate();
            })
        })
    </script>
</body>

</html>
```


#### Back end depolyment

> POST: https://vaptcha.4u0.com/verfiy
>
> Content-Type:  application/json

##### Request

``` javascript
 {
   "scene": 0, 
   "token":"***", //Obtained after verification
   "server": "***", //Obtained after verification
   "ip": "***", //Get user's  remote address
 }
```


##### Response

``` javascript
 {
   "success": 1, // Result,"1" means pass, "0" means fail 
   "score": 60, 
   "msg": "0,1,2,3" 
 }
```
