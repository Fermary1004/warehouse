# 5/9

## 환경 설정

### 접속 정보
- mac : 사번/1
- Pnet : 사번/Skp네번호별
- 메일주소 : omwomw@sk.com
- Sk Happy Express : omwomw/Skp사번

- 키보드
	- Karabiner로 한/영 전환 설정
	- Karabiner로 왼쪽 CTRL <=> Command 서로 바꾸기
	- Spotlight 단축키를 CTRL + Space 로 변경
- skitch 설치
	- 화면 캡쳐 - 에버노트 연동
- MacDown 설치
- 업데이트
	- OS : El Capitan 10.11.4


## 행정

- ID 카드 발급
- 복리후생 계좌 등록
- 메일주소 변경
- Pnet 비번 변경

# 5/10

## 환경 설정

- 유선전화 신청 > OneClick > 사무지원신청 에서 품의 - done
	- 02-6119-3691

## 행정
- 개인형 법인카드 발급 신청 - done
- 명함 신청 - 사내 메일from조형욱 참고 - done
	- 크롬에서는 조회 버튼 팝업 창이 화면 아래에 뜨므로 스크롤 해서 확인 필요
- 개인서랍장 비번 재설정 - done
- slack 가입
	- skp1.slack.com
- hrd.learningmate.co.kr - done
	- http://skplanet.learningmate.co.kr/ - done
	- skp사번전체 // 별표포함
- sk-planet-com.socialcast.com - done
	- 비번  : 별표포함
- www.smartgroupware.com - done
	- omwomw // Skp1003604
- CDP 둘러보기, 직무 선정

## 업무
- http://wiki.skplanet.com/pages/viewpage.action?pageId=91877862 구경


# 5/11

## 환경 설정

- S-VDI 신청 - 인사팀에서 DB 안 남어와서 로그인이 안됨 5/11에 다시 신청
	- 신청 화면은 들어와져서 신청 및 승인 완료
	- 실제 접속은 익일에 가능
- 첨부문서 열어보기 보안 해제
	- OA-VDI 신청
- realforce 키보드 
	- karabiner 설정
		- 한영 전환(shift+space), 화살표키 블록지정
- OA-VDI 신청
	- Pnet > 업무시스템 > IT요청/단말/SW신청 > S/W신청 > S/W사용신청 의 Information에 나오는 담당자에게 OA-VDI 신청 문의하면 대신 신청해줌
	- 신청 승인 완료되면 알림 메일 보내줌

## 행정
- http://skplanet.learningmate.co.kr/ - done
	- skp사번전체 // 별표포함
- 인사팀 최지혜에게 인사기록카드 출력본, 대학원 성적증명서 출력본 제출
- e-HR에 가족 정보 등록

# 5/12

## 환경설정

- OA-VDI 접속
- 개발자 모니터 추가 신청(나중에 필요시 신청하기로)
- Mac Alfred 설치
- 박용권M에게 사번 알려주고 BitBucket 등록 요청

## 주례회의

## Sprint 종료

## 업무 안내 with 용권

### 주요 URL
- DMP 대쉬보드 : http://dmp.skplanet.com/
- DMP Collector : http://dmp-collect.skplanet.com/
- BitBucket : http://bitbucket.skplanet.com/
- PMon(성능) : https://pmon.skplanet.com/
- LMon(로그) : lmon.skplanet.com (세정M에게 문의)

### 코드 베이스
- BitBucket : http://bitbucket.skplanet.com/
	- DMP
		- engine : 하둡에 올라갈 코드 모음
		- service : 실제 운영 중인 내부 서비스
			- dmp-collector 폴더(성용M도 뭔가 작업)
			- recommend-viewer : 추천 엔진 비교

### 업무

- 채널내상품관리 - Item Manager - QueryCache - Item DB 개발
- 11번가의 상품 목록 변경을 DIC에 반영?
	- 11번가 카운터파트 : 조경태M(차주 합류?)
- 6/24까지
- MR을 jar로 묶어서 HDFS에 올리거나, Job으로 만들어서 Ozzie에 등록하거나, QueryCache를 이용하거나
	- QueryCache
		-  DIC 팀에서 제공해주는 Lib
		- JDBC와 유사한 역할(target이 RDB가 아닌 HDFS)
		- wiki에 관련 자료 있음

### Domain 지식

#### 광고 분야 용어

- DSP : 광고주 쪽
- DMP : DSP 와 SSP 사이에서 최적의 biding을 가능하게 해주는 데이터 서비스
- SSP : 소비자 쪽(신문사, SNS, 일반사용자)
	
#### DMP
- 1차 목표 : FingerPrinting
	- A, B, .. 사이트의 비실명 데이터를 분석해서, A의 a가 B의 b와 같은 사람이다(또는 같은 성향을 가진 사람이다) 도출
	- 분석의 기준이 되는 Key 관리
		- A의 등산용품이라는 카테고리가 B에서도 동일하게 등산용품이라고 불리는 지 알 수 없다. 또는 같은 등산용품이라고 해도 하위 카테고리가 다를 수도 있다.. 
	- 사용자 식별
	- FingerPrinting
- 2차 목표 : 식별된 개인에 대한 교차 추천
	- 1차 목표의 식별 결과를 바탕으로, A, B, ..를 이용하는 사용자 `갑`에게 A에서는 보지(사지) 않고 B에서는 봤던(샀던) 항목을 A에서도 추천하고, B에서는 보지(사지) 않고 A에서는 봤던(샀던) 항목을 추천
	- 교차추천에 대한 A/B Test Framework 개발
- Data Source(DMP Client)
	- 11번가, SyrupAD, RecoPick의 고객사이트들
- DMP는 코어 역할, DaaS는 서비스, 추천 A/B는 브랜치 같은 역할

#### Segment(세그)
- 구분 기준 항목?
	- 예) 20대 남성 스포츠	

# 5/13

## 개발 환경
- BitBucket
- SKValley : 배포 시스템(Jarvis)
	- skvalley.com
		- 로그인 > 계정생성
		- Pnet아이디있으세요? Yes
		- Pnet 사번/비번 입력
		- 자동입력된 화면 확인 후 '계정 생성' 클릭
		- skp사번 으로 ID 생성됨
- DB서버접근, 서버접근제어
	- 접근 서버 목록은 위키에 있는 모든 서버
	- 보안포털에서 신청
		- IDC : 일산
		- 기간 : 1년
		- 내용 : DMP 프로젝트 개발
	- 위키에 서버접근, DB서버접근 엑셀 템플릿 추가
- iVPN 접속
	- ivpn.skplanet.com
		- 화면 하단 Mac사용자 가이드 참고하여 설정
- 터미널 alias
	
	```
	vi ~/.profile
	
	alias ll='ls -al'
	```
- JDK
	- Oracle 사이트에서 dmg 파일 다운받아 설치
	- Sublime Text 3 설치
		- https://www.sublimetext.com/3 에서 설치
		- 다운로드 받은 dmg 파일을 더블클릭하면 Applications를 지정하는 소프트링크 폴더와 SublimeText 실행기 두개가 표시되는데, 
		- 같은 창에서 실행기를 Applications 폴더에 드래그드랍하면 LaunchPad에 등록됨
	- 명령어 라인 도구 설치
		- git --version 을 실행하면 명령어 라인 도구 설치를 묻는 화면오며, 화면 안내에 따라 설치한다.
	- Homebrew
		- http://brew.sh/index_ko.html에 있는대로 설치
			- 일부 문서에서는 XCode가 있어야 Homebrew를 설치할 수 있는 것으로 설명하고 있으나, HomeBrew는 XCode 없이 명령어 라인 도구만 있어도 설치 가능
		- 설치 완료 후 `brew doctor`를 실행해서 정상 설치 여부 확인
- IntelliJ 설치
	- 인텔리제이 홈페이지에서 dmg 파일을 다운받아 더블 클릭
	- 인텔리제이 실행기를 옆에 있는 Applications 폴더로 드래그드랍
- 개발 소스 구성
	- 로컬에 Parent Directory 생성

		```
		cd ~
		mkdir -p gitRepo/dmp
		```
	- 인텔리제이
		- Clone
			- 소스 repo URL : bitbucket.skplanet.com > DMP > service 에서 좌측의 ... 클릭 > Clone 클릭하면 URL 표시됨
			- Parent Directory로  `gitRepo/dmp` 지정
		- SDK 지정
			- Java Home 확인
				
				```
				/usr/libexec/java_home -v 1.8
				```
		- Directory Marking
			- src/main/java, src/main/resources
			- src/test/java, src/test/resources
		- Gradle 적용
			- 인텔리제이를 껐다 켜면 자동으로 감지하며 Ok로 적용(최초에는 의존성 구성에 시간이 많이 걸림)

## 업무

- Wiki에 서버/DB서버 접근 제어 신청 엑셀 템플릿 등록
- JIRA 사용법
	- 스크럼 보드 : Agile > DMP Scrum Board
		- Backlog : 티켓 목록
		- Active sprints : 티켓 진행 단계(ToDo, In Progress, Done)
		- Reports : Burndown Chart
	- 작성 시 이슈 링크하는 법
	

# 5/16

## 환경 설정

- DB 접근 제어 테스트
- 서버 접근 제어 확인

## 업무

### 문의 필요

- 

# 선택적복리후생 사용 내역

- 11번가
	- 5/9 지압 슬리퍼
	- 5/10 충전기, ~충전케이블~, 마우스 패드
	- 5/11 한일선풍기
	- 5/13 충전케이블
- 다이소
	- 5/10 다이소 - 북스탠드


# 이직자 처리 순서

## 1일차
1. Mac에 가상 윈도우(Parallels) 설치 요청
	- 02-2196-5100 으로 요청
1. ID카드 용 사진 촬영 예약
	- 점심 시간 후로 예약하고 촬영하면 당일에도 ID 카드 발급 가능
1. Mac 키보드 설정(필수 아님)
	- Karabiner 설치 후 설정
1. 복리후생 계좌 등록
	- Pnet > e-HR > 복리후생/기타  > 복리후생내역 조회/신청 에서 계좌번호 등록
	- 복리후생 계좌를 등록한 다음날 개인형법인카드 신청가능하므로 첫날에 하는 것이 좋음
1. 개인 사물함 비밀번호 설정
	- Pnet > 사내지원 > OneStop서비스
	- 분류 : Key요청(서랍, 회의실)
	- 사옥명, 근무층, 제목, 요청내용 등 작성
1. 유선 전화 신청
	- Pnet > 사내지원 > 사무지원신청
		- 설치장소 : The planet, 8층
		- 설치유형 : 유선전화
		- 완료요청일 : 당일
		- 요청내역 : 업무용 유선전화 설치 요청
		- 요청사유 : 업무용 유선전화 사용
1. OA-VDI 신청
	- Mac에서 가상 윈도우에 접속 가능한 환경
		- Mac에서는 Pnet등에서 다운받은 파일을 열어볼 수 없으며, OA-VDI 내의 윈도우 환경에서 Pnet에 접속해서 다운받아 열어볼 수 있음
	- Pnet > 업무시스템 > IT요청/단말/SW신청 > S/W신청 > S/W사용신청 의 Information에 나오는 담당자에게 OA-VDI 신청 문의하면 대신 신청해줌
1. 통근버스 확인
	- Pnet > 사내지원 > 기타지원서비스 > 통근버스
1. Pnet 이메일/비밀번호 변경
	- Pnet 좌상단 '설정' 클릭
	- 이메일 변경은 익일에 반영되고, 변경 신청 후 메일 수신이 안되는 경우도 있으므로, 각종 처리를 모두 마친 후 퇴근 전에 변경하는 것이 좋음

## 2일차

1. 메일 주소 변경 확인
	- 외부 메일로 메일 수발신 확인
1. 명함 신청
	- 명함신청 용 ID 신청
		- Pnet > 사내지원 > 기타지원서비스 > 명함신청
		- 우상단의 Notice 에 있는 내용대로 담당자에게 이메일로 신청
	- Pnet > 사내지원 > 기타지원서비스 > 명함신청
1. 개인형법인카드 신청
	- 입사 후 안내 메일 참고
1. OA-VDI 접속
	- vdi.skplanet.com
	- 문서보안 초기 비밀번호 : 사번
	- 화면에 나오는대로 몇가지 보안 프로그램 설치
	- 네이트온비즈는 별도 로그인 절차 없이 자동 로그인됨
1. S-VDI 신청
	- Pnet > 업무시스템 > 보안포털 > S-VDI/PTS 신청
		- 설정 내용은 Buddy 에게 문의
		- 신청 후에도 security portal의 최근 신청내역 이 0으로 나오더라도, Pnet > 업무시스템 > IDMS(통합ID관리시스템) > 계정 신청 > 좌측 신청처리업무 > 계정신청 에서 신청 목록 나오면 신청된 것임
	- S-VDI/PTS 화면 이동 시 세션 정보가 없다는 에러가 뜨면  Pnet > 업무시스템 > 보안포털 > 우하단의 담당자 안내 를 클릭해서 유선으로 문의 후 처리

## 3일차

1. S-VDI 접속
	- 신청 승인 완료 후 익일에 초기비밀먼호 포함된 알림메일 수신
	- Pnet > 업무시스템 > 보안포털 > S-VDI 접속 에서 접속 

