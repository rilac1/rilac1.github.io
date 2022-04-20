---
title: "우분투에서의 MongoDB 설치 및 활용"
date: 2021-07-12 12:00:00 +/-TTTT
categories: [DB, MongoDB]
tags: [MongoDB]     # TAG names should always be lowercase
---

## 1. Ubuntu에 MongoDB 설치하기
[공식 홈페이지](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition-using-deb-packages)

#### 1-1. GPG 키 import
```sh
wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
```
#### 1-2. MongoDB 리스트 파일 만들기
- 20.04
```sh
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```
```sh
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```
#### 1-3. 로컬 패키지 불러오기
```sh
sudo apt-get install -y mongodb-org
```

## 2. MongoDB 실행
#### 2-1. MongoDB 동작시키기
```sh
sudo systemctl start mongod
```
#### 2-2. 잘 동작하는지 확인
```sh
sudo systemctl status mongod
```
![sudo systemctl status mongod](https://user-images.githubusercontent.com/67721382/125254453-70318f00-e335-11eb-9314-0d0913b65191.PNG)
#### 2-3. Mongo Shell
```sh
mongo
```
---
## 3. 계정 생성
MongoDB의 기본적으로 27017포트를 사용합니다. 하지만 MongoDB 서버에 포트만 열려있으면 보안에 매우 취약하기 떄문에 계정을 생성하여 사용하겠습니다.

#### 3-1. admin 유저 생성
일반 계정을 생성하기 위해서는 먼저 admin 계정을 생성해야 합니다.
> adminDB에 "admin" 유저 생성

```sh
mongo> use admin
mongo> db.createUser(
    {
    user: "admin",
    pwd: passwordPrompt(), // or cleartext password 
    roles: [ "root" ]
    })
```
#### 3-2. 일반 계정 생성
> myDB에 "usr" 유저 생성

```sh
mongo> use myDB
mongo> db.createUser(
    {
    user: "usr",
    pwd: passwordPrompt(),
    roles: [ "readWrite" ]
    }
)
```
> 계정 삭제

```sh
mongo> use myDB
mongo> db.dropUser("usr")
```

#### 3-3. 보안 설정
```sh
sudo vim /etc/mongod.conf
```
`/etc/mongod.conf`에서 security 부분의 주석을 제거하고, 아래 옵션을 넣어줍니다.


`security: authorization: enabled`


---
## 4. VScode Extension을 활용한 GUI 사용
#### 4-1. MongoDB 주소 확인
    ```
    $ mongo
    ```
    ![MongoDB 주소](https://user-images.githubusercontent.com/67721382/125260235-17fd8b80-e33b-11eb-8cac-d1948f4b21df.png)

#### 4-2. extension "MongoDB for VS Code" 설치  
    ![](https://code.visualstudio.com/assets/docs/azure/mongodb/install-cosmosdb-extension.png)

#### 4-3. Connect
    ![](https://user-images.githubusercontent.com/67721382/125259569-65c5c400-e33a-11eb-89fa-077945d3b826.png)

#### 4-4. 확인한 주소 입력
    ![캡처](https://user-images.githubusercontent.com/67721382/125259888-bb9a6c00-e33a-11eb-9f92-d053e0ad55d1.PNG)

#### 4-5. 이후 `Ctrl+Shift+P`-"MongoDB: Connect"의 Advanced Connection Settings 를 클릭하여 Authentication을 Username/Password로 설정해줍니다.
---

## 5. MongoDB 사용하기
GUI를 사용하면 Collection을 만들고 삭제할 수는 있지만 Collection 내부의 Data는 삭제가 불가능한 것 같습니다. 따라서 내부 데이터를 삭제하는 간단한 명령어만 소개하겠습니다.
#### 5-1. 데이터베이스 리스트 확인
```sh
mongo> show dbs
```
#### 5-2. 사용할 데이터베이스 선택 / 생성
```sh
mongo> use myDB
```
#### 5-3. 데이터 조회
```sh
mongo> db.bookreview.find({title: '열혈 C'})
```
#### 5-4. 데이터 삭제
```sh
mongo> db.bookreview.remove({title: '열혈 C'})
```
