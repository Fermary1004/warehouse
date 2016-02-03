# 다른 계정의 S3로 이사가기

AWS 프리티어 기간 만료가 가까워서 새 계정을 개설해서 옮겨보려고 한다.
마침 AWS에 서울 리전(Region)이 개설되었고, S3도 [개설 서비스 목록](http://www.awsseoul.kr/sub01.html#introseoul02)에 포함되어 있다.

![](http://i.imgur.com/4mcAI8G.png)

새 계정의 S3로 이사가는 대략적인 큰 순서는 다음과 같다.

1. AWS 새 계정 생성, IAM Group 생성, IAM 계정 생성
2. credentials 파일에 새 IAM 계정 정보 추가
3. config 파일에 profile 정보 추가
4. 새 계정의 S3에 버킷 생성
5. 기존 S3 버킷 권한 수정
6. 새 계정의 Custom Policy 설정
7. AWS CLI 설치
8. aws sync 명령 실행

이 중에서 1번은 AWS 사용자라면 다들 알만한 내용이니 과감히 뛰어넘고 2번부터 정주행하자.

# credentials 파일에 새 IAM 계정 정보 추가



# config 파일에 profile 정보 추가

# 새 계정의 S3에 버킷 생성

아래와 같이 Region에 Seoul을 지정하고 Create를 클릭

![](http://i.imgur.com/DNQpVSN.png)

# 기존 S3 버킷 권한 수정

# 새 계정의 Custom Policy 설정

# AWS CLI 설치

# aws sync 명령 실행
