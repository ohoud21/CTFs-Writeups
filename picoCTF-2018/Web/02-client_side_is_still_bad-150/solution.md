# Problem
I forgot my password again, but this time there doesn't seem to be a reset, can you help me? [http://2018shell1.picoctf.com:55790](http://2018shell1.picoctf.com:55790)

## Hints:
Client Side really is a bad way to do it.

## Solution:

Lets look at the source of the website.
```bash
curl http://2018shell1.picoctf.com:55790

<html>
<head>
<title>Super Secure Log In</title>
</head>
<body bgcolor="#000000">
<!-- standard MD5 implementation -->
<script type="text/javascript" src="md5.js"></script>

<script type="text/javascript">
  function verify() {
    checkpass = document.getElementById("pass").value;
    split = 4;
    if (checkpass.substring(split*7, split*8) == '}') {
      if (checkpass.substring(split*6, split*7) == 'd366') {
        if (checkpass.substring(split*5, split*6) == 'd_3b') {
         if (checkpass.substring(split*4, split*5) == 's_ba') {
          if (checkpass.substring(split*3, split*4) == 'nt_i') {
            if (checkpass.substring(split*2, split*3) == 'clie') {
              if (checkpass.substring(split, split*2) == 'CTF{') {
                if (checkpass.substring(0,split) == 'pico') {
                  alert("You got the flag!")
                  }
                }
              }
      
            }
          }
        }
      }
    }
    else {
      alert("Incorrect password");
    }
  }
</script>
<div style="position:relative; padding:5px;top:50px; left:38%; width:350px; height:140px; background-color:red">
<div style="text-align:center">
<p>Welcome to the Secure Login Server.</p>
<p>Please enter your credentials to proceed</p>
<form action="index.html" method="post">
<input type="password" id="pass" size="8" />
<br/>
<input type="submit" value="Log in" onclick="verify(); return false;" />
</form>
</div>
</div>
</body>
</html>
```

We can see the code validating the password, and we can reconstruct it.

Simple script:
```python
#!/usr/bin/env python

import requests

r = requests.get('http://2018shell1.picoctf.com:55790')
lines = r.text.split('\n')

lines = [l for l in lines if 'if ' in l]
lines = [l.split('==') for l in lines]

s = ''
for l in lines[::-1]:
  s += l[1].split('\'')[1]

print s
```

Flag: picoCTF{client_is_bad_3bd366}