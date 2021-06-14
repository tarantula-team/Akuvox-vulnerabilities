# Akuvox-vulnerabilities

## RCE in CFcgiController::HttpSetAccoutDisplayName
The web service (port 80) on Akuvox C315 115.116.2.613 has a remote code execution. In details, the function CFcgiController::HttpSetAccoutDisplayName (in libservlets.so) uses strcpy unsafely, with the source string fully controlled by the attacker, leading to stack overflow. An un-authenticated attacker with access to the web service can get code execution on the device.

![image: vulnerable code in CFcgiController::HttpSetAccoutDisplayName](https://github.com/tarantula-team/Akuvox-vulnerabilities/blob/main/image/RCE-in-HttpSetAccountDisplayName.PNG?raw=true "Vulnerable code in CFcgiController::HttpSetAccoutDisplayName")

PoC triggering crash:
```Python
#/usr/bin/python3
Import requests

target_ip = '172.28.83.57'
url = ('http://%s/fcgi/do?displayname='+'A'*1000) % target_ip
r = requests.get(url)
print(r.status_code)
```
Crash while debugging in gdb, with PC register going to be ovewritten with arbitrary value:

![image: crash in gdb with PC controlled](https://github.com/tarantula-team/Akuvox-vulnerabilities/blob/main/image/RCE-gdb-HttpSetAccoutDisplayName.PNG?raw=true "Crash in gdb with PC controlled")

## RCE in CNetworkBModel::GetIPArray
The web service (port 80) on Akuvox C315 115.116.2.613 has a remote code execution. In details, in the function CNetworkBModel::GetIPArray (in libservlets.so), the second argument of snprintf is wrongly used as the size of the forth argument, while it should be the maximum size of target buffer:

![image: vulnerable code in CNetworkBModel::GetIPArray](https://github.com/tarantula-team/Akuvox-vulnerabilities/blob/main/image/RCE-GetIPArray.PNG?raw=true "Vulnerable code in CNetworkBModel::GetIPArray")

This leads to stack overflow, which could result to a remote code execution for an authenticated attacker.
Request triggering the bug:

![image: request triggering the bug](https://github.com/tarantula-team/Akuvox-vulnerabilities/blob/main/image/RCE-request-GetIPArray.png?raw=true "Request triggering the bug")

Crash while debugging in gdb, with PC register controlled:

![image: crash in gdb with PC controlled](https://github.com/tarantula-team/Akuvox-vulnerabilities/blob/main/image/GetIPArray-crash-in-gdb.PNG?raw=true "Crash in gdb with PC controlled")

