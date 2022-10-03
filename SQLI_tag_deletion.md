# SQL injection vulnerability in OpenCats 'Tag' deletion functionality.

OpenCats version 0.9.6 PHP7.2 suffers from SQL injection vulnerability. This allows attackers control over the application's database.

User has control over tagID variable, which allows SQL injection in DELETE statement via time-based/blind techniques.

PoC:
![1](https://user-images.githubusercontent.com/57464251/193554662-3ff9fd88-a6fd-46c6-be8e-d6ff32f7fb11.png)

![2](https://user-images.githubusercontent.com/57464251/193554677-ff4fbf7d-fd69-4e56-9da7-81875ae88b95.png)

![3](https://user-images.githubusercontent.com/57464251/193554689-d1aed402-e78d-4202-ab8b-f354df33cbf7.png)

![4](https://user-images.githubusercontent.com/57464251/193554697-20965e88-df36-4e9b-be18-ce92f1ed08ce.png)

![5](https://user-images.githubusercontent.com/57464251/193554702-419d861b-6a3d-4de4-b70d-81964d7cc23b.png)

tagID variable is vulnerable.
Similar query is build by application:
`DELETE FROM tag WHERE(tag_id = XXX OR tag_parent_id = 1) AND site_id = 1;`

User can control XXX part. By asking many yes/no questions to the database user can differentiate between true and false statements.
Using time-based blind technique attacker can exfiltrate data from entire database.

### PoC example: 
exfiltrate result of database() query:

https://user-images.githubusercontent.com/57464251/193555221-19bdd56f-f11c-425c-ac73-8dddaf3b9078.mp4
