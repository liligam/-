# 1. Jmeter 설치

본 문서는 Jmeter 5.1.1버전(Java 8버전 이상에서 지원)으로 작성되었다.
	설치 파일 다운로드
-	http://mirror.navercorp.com/apache//jmeter/binaries/apache-jmeter-5.1.1.zip
	Jmeter 실행
-	{압축 해제 경로}\bin\jmeter.bat 실행
	여기서 잠깐!! PC에 개발환경이 구성되어 있어 JAVA_HOME의 jdk 버전이 1.6이여서 실행이 안되는 경우, jmeter실행파일에서jdk의 minimal 버전을 1.6으로 변경 후1.8버전 이상의 jdk 경로를 수동으로 설정하시기 바랍니다.
아래는 PC의 JAVA_HOME이 1.6으로 설정되어 있고, jdk 1.8을 별도로 설치했을때의 예시이다.

1) (예시), {압축 해제 경로}\bin\jmeter.bat 파일의 80번째 줄 수정
   set MINIMAL_VERSION=1.6.0

2) (예시), {압축 해제 경로}\bin\jmeter.bat 파일의 132번째 줄 수정
   set JM_LAUNCH=C:\Program Files\Java\jdk1.8.0_271\bin\java.exe


## 1-2. Jmeter Recording설정
Jmeter의 TestPlan (테스트할 기능 레코딩)이 이미 작성되어 있는 경우에는 이 과정을 생략하고 “3. Jmeter 테스트 방법”을 진행합니다.
Jmeter Recording을 위해서는 Jmeter의 설정과 테스트를 진행하는 PC의 프록시 설정이 필요합니다.
※	여기서 잠깐!! cloudium의 RPC프로토콜이 5.19.04에서 XML에서 메시지팩으로 변경된 뒤 Jmeter의 Recording이 동작하지 않으므로, 5.19.04보다 이전 버전에서만 진행하시기 바랍니다.
	Jmeter Recording 설정
-	PC와 서버간 http 요청 행위를 기록해서 해당 행위에 대한 반복 테스트를 진행할 수 있다.
![image](https://user-images.githubusercontent.com/79567212/218961768-0e8765ca-f869-4f13-9c82-89e46cd7d5ac.png)

![image](https://user-images.githubusercontent.com/79567212/218962160-a7910036-6e72-469f-9568-63353a43ab28.png)

*hostToRecord: {테스트할 서버 IP}
*recordingOutoutFile: {테스트 시나리오명}.xml
*schemeToRecord: {통신 프로토콜 명, 특별한 경우가 아니라면 “http”로 입력}

![image](https://user-images.githubusercontent.com/79567212/218962257-74362876-84ee-44b0-acdb-fe8a7590b2ae.png)

-	HTTP(S) Test Script Recorder 설정

![image](https://user-images.githubusercontent.com/79567212/218962320-6cb3e801-a187-4cdf-99a7-948dac24b35f.png)

*Port: {PC에서 Recording에 사용할 포트번호, 기본값 “8888”}
*Target Controller: {레코딩이 되는 http 요청이 기록될 위치}
*“Start” 클릭 -> 테스트용 인증서 생성 알림 화면에서 “OK” 클릭

	PC 프록시 설정
-	PC에서 서버로 가는 요청을 Jmeter가 인식할 수 있도록 위 단계에서 지정한 포트번호와 동일한 번호로 프록시 설정을 한다.
-	제어판 -> 인터넷 옵션 -> 프록시 서버 구성 -> 연결 탭 -> LAN 설정(L) -> “자동으로 설정 검색” 체크 해제 -> “사용자 LAN에 프록시 서버 사용” 체크 -> “주소: 127.0.0.1 / 포트: 8888”로 작성 -> 확인
 


## 1-3.	Jmeter 테스트 방법

	Sessionkey 생성
-	로그인 테스트를 위해 rpcLogin API를 사용해 각 테스트 계정 마다의 sessionkey를 생성하고, 생성된 키 값을 session.csv 파일 C열에 입력한다.
-	 Thread Group_S1_0_make_sessionkey 설정

Number of Threads (users): {session.csv파일에 기록되어 있는 유저수}
Ramp-Up Period (inseconds): {반복 테스트를 진행하는 시간, 특이사항이 없으면 600으로 설정}
Loop Count: {반복 횟수, 특이사항이 없으면 1로 설정}
-	위와 같이 설정 후 “초록색 화살표 버튼” 클릭
-	http://192.168.1.190/S1/moncache.jsp 페이지에 접속하여 “userSessionIDListCache” 항목을 이용해 각 account마다의 세션 키 값을 session.csv 파일 C열에 입력한다.
 
	테스트 진행
-	“Thread Group_S1_0_make_sessionkey” 항목을 비활성화하고 나머지 항목을 활성화 한뒤 sessionkey 생성을 할 때와 같이 서버로의 요청 횟수를 지정하고 초록색 화살표 버튼을 클릭한다. (활성화 / 비활성화 방법: “Thread Group_S1_1~5… …” 우클릭 Enable / Disable)

	테스트 결과 확인
-	TestPlan 하위에 Summary 리포트에서 결과를 확인하고 화면 캡쳐를 한 뒤, 각 기능별 응답시간(Min / Max / Average)을 엑셀 장표에 기입한다.
-	아래 엑셀파일은 테스트 결과를 작성하는 예시이다. 


