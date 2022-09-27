
# SQL injection vulnerability in OpenCats 'Job Orders'

OpenCats version 0.9.6 PHP7.2 suffers from SQL injection vulnerability.
This allows attackers control over the application's database.

User has control over entriesPerPage variable, which allows SQL injection in UPDATE statement, setPipelineEntriesPerPage function call.

SQL query code:

![Code](https://user-images.githubusercontent.com/57464251/192616786-d77f2f1a-a944-442b-a0a1-9bfeb45aea02.png)

Since UPDATE statement is used to query the database, user can add arbitrary values to arbitrary columns inside 'user' table.
Knowing this, it is possible to craft payload like:
`15,first_name=(select password from user where user_id=1 limit 1)`  

This will update 'first_name' with arbitrary data from database. In this example user's password hash will be written inside first_name column.
Since, first name is reflected in many endpoints in application, this means malicious person can exfiltrate data and control the database using it as a field to extract data. Attackers can also use blind sql injection techniques to extract db information.

![req](https://user-images.githubusercontent.com/57464251/192617442-b2d25b34-0fa4-4493-9bd6-7c59eb6411bc.png)

![poc](https://user-images.githubusercontent.com/57464251/192617758-e6912c3f-f7b5-40e9-b4cc-2d49a6f8d5cf.png)
