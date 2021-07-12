---
title: "[Ubuntu] 우분투에서의 MongoDB 설치 및 활용"
date: 2021-07-12 12:00:00 +/-TTTT
categories: [DB, MongoDB]
tags: [MongoDB]     # TAG names should always be lowercase
---

## Ubuntu에 MongoDB 설치하기
[공식 홈페이지](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition-using-deb-packages)

1. GPG 키 import
    ```
    $ wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
    ```
    ```
    OK
    ```
2. MongoDB 리스트 파일 만들기
    - 20.04
    ```
    $ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
    ```
    - 18.04
    ```
    $ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
    ```
3. 로컬 패키지 불러오기
    ```
    $ sudo apt-get install -y mongodb-org
    ```

---
## MongoDB 실행
1. MongoDB 동작시키기
    ```
    $ sudo systemctl start mongod
    ```
2. 잘 동작하는지 확인
    ```
    $ sudo systemctl status mongod
    ```
    ![sudo systemctl status mongod](https://user-images.githubusercontent.com/67721382/125254453-70318f00-e335-11eb-9314-0d0913b65191.PNG)
3. MongoDB 사용하기
    ```
    $ mongo
    ```
    ![mongo](https://user-images.githubusercontent.com/67721382/125254670-a4a54b00-e335-11eb-931e-66e79a555fd0.PNG)

---
## VScode Extension을 활용한 GUI 사용
1. extension "MongoDB for VS Code" 설치
![VS Code](https://user-images.githubusercontent.com/67721382/125255396-55abe580-e336-11eb-913b-3d3f99a4a58f.PNG)

2. MongoDB 주소 확인
    ```
    mongo
    ```
    ![MongoDB 주소](https://user-images.githubusercontent.com/67721382/125255821-c9e68900-e336-11eb-9cd1-92dd240e42a3.png)

3. 확인한 주소 입력하면 끝!
    ![캡처](https://user-images.githubusercontent.com/67721382/125256454-6ad54400-e337-11eb-83ec-9452e073e972.PNG)

## MongoDB 사용하기
> GUI를 사용하면 Collection을 만들고 삭제할 수는 있지만 Collection 내부의 Data는 삭제가 불가능한 것 같습니다.  
> 따라서 내부 데이터를 삭제하는 간단한 명령어만 소개하겠습니다.

1. 사용할 데이터베이스 선택 / 생성
    ```
    > use test      # use [DB 이름]
    ```
2. 데이터베이스 리스트 확인
    ```
    > show dbs
    ```
3. 데이터 조회
    ```
    > db.bookreview.find({title: '열혈 C'})       # db.[Collection 이름].find()
    ```
4. 데이터 삭제
    ```
    > db.bookreview.remove({title: '열혈 C'})     # db.[Collection 이름].remove()
