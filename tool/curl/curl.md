# curl
- [curl](#curl)
  - [登录获得auth](#登录获得auth)
  - [使用auth进行下载, 上传](#使用auth进行下载-上传)

本文章主要记录一下, 常用的curl命令, 我在服务器安装了一个[filebrowser](https://github.com/filebrowser/filebrowser), 我想使用curl进行下载上传

## 登录获得auth
登录filebrowser, 并把auth储存到本地

```
curl -o auth.txt -d '{"username":"kkk","password":"123456","recaptcha":""}' "http://localhost:8085/api/login" 
```

## 使用auth进行下载, 上传

下载

```
curl --header 'X-Auth: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7ImlkIjoxLCJsb2NhbGUiOiJlbiIsInZpZXdNb2RlIjoibGlzdCIsInNpbmdsZUNsaWNrIjpmYWxzZSwicGVybSI6eyJhZG1pbiI6dHJ1ZSwiZXhlY3V0ZSI6dHJ1ZSwiY3JlYXRlIjp0cnVlLCJyZW5hbWUiOnRydWUsIm1vZGlmeSI6dHJ1ZSwiZGVsZXRlIjp0cnVlLCJzaGFyZSI6dHJ1ZSwiZG93bmxvYWQiOnRydWV9LCJjb21tYW5kcyI6W10sImxvY2tQYXNzd29yZCI6ZmFsc2UsImhpZGVEb3RmaWxlcyI6ZmFsc2UsImRhdGVGb3JtYXQiOmZhbHNlfSwiaXNzIjoiRmlsZSBCcm93c2VyIiwiZXhwIjoxNjcyODkwMjMxLCJpYXQiOjE2NzI4ODMwMzF9.5r-oPIR-utCaET0z3zobDfcBF73dECbdOSk-1xehIM8' "http://localhost:8085/api/resources/root/tmp/logs/0dc3ecb052f68919b93bd73bfd520f7.png" -O 
```

上传好像不需要直接使用界面上传
