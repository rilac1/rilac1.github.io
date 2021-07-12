---
title: "[Ubuntu] MongoDB 설치 및 활용"
date: 2021-07-12 12:00:00 +/-TTTT
categories: [DB, MongoDB]
tags: [MongoDB]     # TAG names should always be lowercase
---

## Ubuntu에 MongoDB 설치하기
https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition-using-deb-packages

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
    ![캡처](https://user-images.githubusercontent.com/67721382/125254453-70318f00-e335-11eb-9314-0d0913b65191.PNG)
3. MongoDB 사용하기
    ```
    mongo
    ```
    ![캡처2](https://user-images.githubusercontent.com/67721382/125254670-a4a54b00-e335-11eb-931e-66e79a555fd0.PNG)

---
## VScode Extension을 활용한 GUI 사용
1. extension "MongoDB for VS Code" 설치
![3](https://user-images.githubusercontent.com/67721382/125255396-55abe580-e336-11eb-913b-3d3f99a4a58f.PNG)

2. MongoDB 주소 확인
    ```
    mongo
    ```
    ![제목 없음](https://user-images.githubusercontent.com/67721382/125255821-c9e68900-e336-11eb-9cd1-92dd240e42a3.png)

3. 확인한 주소 입력하면 끝!