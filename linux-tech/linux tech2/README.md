# 오라클 아카이브 모드 적용 및 복구 매뉴얼

#1-1.	컨트롤 파일 다중화 구성
컨트롤 파일은 DB가 운영될 때 실시간으로 각종 정보가 저장되고 조회가 된다. 
컨트롤 파일이 삭제될 경우, 심각한 문제가 발생하기 때문에 삭제되지 않도록 주의해야 하며, 혹시나 삭제되더라도 복구할 수 있도록 복사본을 만들어 분산시켜 관리하는 것을 멀티플렉싱(다중화 구성) 이라고 한다.

(1)	컨트롤 파일 DB 설정 조회
![image](https://user-images.githubusercontent.com/79567212/218963682-36ed3355-7a76-4d27-837e-8a1bce5a2d69.png)

(2)	컨트롤 파일 DB 설정 다중화 변경
![image](https://user-images.githubusercontent.com/79567212/218963707-e551e998-2f50-4c15-abc0-3c6692743ba3.png)

(3)	DB 종료 후, 컨트롤 파일 변경 적용 (실제 파일 복사) 
![image](https://user-images.githubusercontent.com/79567212/218963721-d9b71a6b-a484-413b-bd78-47b46686bbc3.png)

(4)	DB 기동 후, 변경된 컨트롤 파일 확인
![image](https://user-images.githubusercontent.com/79567212/218963768-0a385c06-870c-4a31-be04-2f260183ab11.png)

#1-2.	아카이브 모드 설정 작업

오라클 DB에서 수행되는 모든 작업은 리두로그(redo log) 파일에 저장이 된다. 리두로그 파일은 작업의 양이 많으면 증가하는 방식이 아니라, 몇 개의 리두로그 파일을 만들고 번갈아 가면서 기록하는 구조로 되어 있다. 이렇게 번갈아 가면서 기록되면, 새로운 작업의 내용이 이전 작업 내용을 덮어쓰게 되므로, DB 장애 시 이전 작업 내용을 복구하기 어려운 단점이 있다. 이런 단점을 해결하기 위한 방법이 아카이브 로그 모드(아카이브 모드 Mode) 이다. 
 아카이브 모드는 리두로그 파일의 내용을 다른 디렉토리에 자동으로 복사해서 저장하도록 운영하는 방식이다.
오라클 DB는 기본적으로 노아카이브 모드(No Archive Mode) 이다. 아카이브 모드로 운영하기 위해서는 아래의 절차대로 설정해야 한다.

(1)	아카이브 모드 현재 상태 조회
![image](https://user-images.githubusercontent.com/79567212/218963981-ed5e4fdd-1fb4-4b43-a9b7-ec644eda81a0.png)

(2)	아카이브 모드 설정
-	아카이브 로그를 저장할 디렉토리 설정
![image](https://user-images.githubusercontent.com/79567212/218964019-c68be7eb-5093-4638-8c9a-9e4db5df6da4.png)


(3)	아카이브 로그 파일 규칙 설정
![image](https://user-images.githubusercontent.com/79567212/218964040-f8ffb800-720b-4474-8637-eca9e5d6a8d4.png)


(4)	DB 종료 후 MOUNT모드로 인스턴스 기동
![image](https://user-images.githubusercontent.com/79567212/218964060-b83c2429-99a6-4ed0-97e0-042375e4ff76.png)

 
(5)	아카이브 모드 정상 설정 확인 후 인스턴스 OPEN
![image](https://user-images.githubusercontent.com/79567212/218964084-a7f0fb15-1564-4dbf-94e8-1568bdec30f2.png)

(6)	강제로 로그 스위치를 발생시켜 아카이브 로그가 저장되는지 확인
 ![image](https://user-images.githubusercontent.com/79567212/218964115-27dadf65-f0df-4413-a10d-64de1b54208a.png)


(7)	아카이브 로그 생성 확인
![image](https://user-images.githubusercontent.com/79567212/218964143-16518af6-f624-4293-b5dc-33e4c410ed57.png)

#1-3. 오라클 백업
(1)	오라클 닫힌 백업 (cold backup)
DB가 종료된 상태에서 수행하는 백업이다. 백업시점이 모두 동일해야 하며, 데이터 파일, 온라인 리두 로그 파일, 컨트롤 파일 백업이 가능하다. 닫힌 백업은 아카이브 / 노아카이브 모드 둘 다 가능하다.

(1)	DB 종료
![image](https://user-images.githubusercontent.com/79567212/218964306-7b2e6a3f-fb2e-44bc-aa83-2db78db49bf9.png)

(2)	컨트롤 파일, 리두 로그 파일, 데이터 파일 백업
-	백업 경로 생성 및 orcl 디렉토리 복사 (경로: /app/oracle/oradata/orcl)
![image](https://user-images.githubusercontent.com/79567212/218964361-fadc8e0c-43d1-4e8b-bf94-2646d573839d.png)

(3)	백업 완료 된 물리파일 확인
![image](https://user-images.githubusercontent.com/79567212/218964377-7f02905a-d6e3-4cea-bb46-54513674968b.png)

(4)	파라미터 파일 백업
-	(2) 에서 생성한 백업 경로로 파라미터 파일 백업
![image](https://user-images.githubusercontent.com/79567212/218964404-aa58c103-0878-4e5e-9302-0f964906805d.png)

(5)	DB 기동
![image](https://user-images.githubusercontent.com/79567212/218964443-39d81eab-748e-4568-92e2-5def7b46f68f.png)

(2)	오라클 열린 백업 (Hot backup)
닫힌 백업과 달리 DB 기동(open) 상태에서도 백업할 수 있으며, 아카이브 모드에서만 수행 가능하다.
많은 양의 아카이브 로그가 발생하는 단점이 있고, 데이터 파일, 컨트롤 파일만 백업이 가능하다.

(1)	아카이브 모드 상태 확인 
![image](https://user-images.githubusercontent.com/79567212/218964494-03ef1ce2-a972-41e0-9f38-3c762b0e83f4.png)


(2)	백업 – 데이터 파일, 컨트롤 파일
-	‘alter database begin backup’로 백업모드 변경 후 백업 수행
![image](https://user-images.githubusercontent.com/79567212/218964528-78473b6f-89a9-4f64-94b2-e44024a4495f.png)

(3)	백업 – Parameter File
![image](https://user-images.githubusercontent.com/79567212/218964593-1598213b-3a6b-40df-a228-bfdc4b8b00c2.png)

(4)	백업 종료
![image](https://user-images.githubusercontent.com/79567212/218964639-c1c5f86a-881b-4e7f-8c52-1b0322975ac6.png)

(5)	백업 종료 확인 (필수)
※ 아래의 STATUS 필드가 NOT ACTIVE 이여야 한다. (ACTIVE 상태 일시 END 백업 실행)
![image](https://user-images.githubusercontent.com/79567212/218964708-aad7af73-3f97-431a-82ef-4d8a23bdec8f.png)

(6)	백업된 물리 파일 확인
![image](https://user-images.githubusercontent.com/79567212/218964736-a001b97c-112f-49a0-bd43-c8f3e7c31037.png)

4.	오라클 복구 (컨트롤 파일 장애)
(1)	 컨트롤 파일 일부 유실(컨트롤 파일의 파일 1개 삭제 시, 깨졌을 시, SCN틀어졌을 시)

1)	CASE 1: 컨트롤 파일 1개 유실
-	컨트롤 파일 유실로 DB Startup 실패 (ORA-00205 에러 발생)
![image](https://user-images.githubusercontent.com/79567212/218964786-3f7d3d3e-fe4b-4697-9ade-cb087d8973c2.png)

컨트롤 파일 물리 경로 확인
![image](https://user-images.githubusercontent.com/79567212/218964829-cbbb38f0-9ad9-4b26-a6db-0f85990cc9ae.png)

2)	CASE2: – 컨트롤 파일 의 SCN 틀어 졌을 시
-	컨트롤 파일 오류로 SCN 번호가 일치하지 않아 DB Startup 실패 (ORA-00214 에러 발생)
![image](https://user-images.githubusercontent.com/79567212/218964865-515e462b-fcdf-438b-bafb-7f0415fce499.png)

3)	복구 절차
-	version 의 번호가 큰 control01.ctl 파일을 02번으로 복사하여 복구
![image](https://user-images.githubusercontent.com/79567212/218964964-b7b1e39c-933d-4fe1-aaff-84c39aea72f2.png)

-	복구 후 Shutdown 및 startup 시 정상 기동 확인
![image](https://user-images.githubusercontent.com/79567212/218964995-41a500e4-9ce5-4a2f-8dfd-1124fcb7da44.png)

(2)	 컨트롤 파일 완전 유실 
(진행 순서: 컨트롤 파일의 완전 유실 -> 최근 백업된 컨트롤 파일로 1차 복구 -> Old 컨트롤 파일 에러(ora-00205) 발생 -> 재 생성 스크립트 작성 -> 스크립트 실행(Recovery) –> 풀 백업 -> 완료)

장애 확인 (컨트롤 파일 장애 로그)
![image](https://user-images.githubusercontent.com/79567212/218965048-bba39bbc-ce1e-43ce-98d1-2e984f85294d.png)


컨트롤 파일 기존 위치 확인 및 1차 임시복구
컨트롤 파일이 없으면 nomount 상태로 복구 진행이 되지 않으므로 최신 백업 본으로 1차 복구
![image](https://user-images.githubusercontent.com/79567212/218965085-960b7bba-fe22-4cec-a231-0a9605d43f67.png)

DB 기동 및 컨트롤 파일 재 생성 스크립트 작성
DB 기동 시 Data file과 scn 번호가 맞지 않아 에러 발생 (정상)
![image](https://user-images.githubusercontent.com/79567212/218965118-3d933419-7536-4f70-96da-868af6c32104.png)

Controlfile 재 생성 스크립트 작성
※ 반드시 신규로 스크립트 생성하여 진행 
– DB 별 TBS, DataFile, Controlfile 정보가 다름
![image](https://user-images.githubusercontent.com/79567212/218965151-63b95ebf-871d-4a84-875a-ea56ae84972d.png)

vi 편집기로 51~73라인, 82라인, 85라인 남겨두고 삭제 후 저장. (공백이 없어야 함)
(스크립트 완성 본 )
![image](https://user-images.githubusercontent.com/79567212/218965205-a66ce9c4-4de3-43eb-8176-078d00666cc9.png)

DB Shutdown 및 컨트롤 파일 복구
![image](https://user-images.githubusercontent.com/79567212/218965247-44ddca6a-dae0-4b5a-bbf2-c65048e41ba9.png)


복구 완료 확인
DB 재 기동 시 에러 없이 기동 확인
![image](https://user-images.githubusercontent.com/79567212/218965285-07c34f2c-d35c-4611-9ed3-da4565b9e045.png)


DB Full 백업
장애 발생시는 복구 완료 후 반드시 백업 진행

(본 문서의 3.(1) 오라클 닫힌 백업으로 진행)


5.	오라클 복구 (Data File )
Data File 장애 복구
장애 확인
기동 시 에러(ora-01110) 발생
![image](https://user-images.githubusercontent.com/79567212/218965347-88744900-a289-471c-863e-cb6043e0ea26.png)


파일이 삭제되어 조회되지 않음 확인.
![image](https://user-images.githubusercontent.com/79567212/218965370-adbd58ae-463d-4aee-8f7b-5ed1a85fad2b.png)

임시 1차 복구
최신 백업파일로 임시 복구
![image](https://user-images.githubusercontent.com/79567212/218965415-de6aa7bb-8954-46ed-9179-c61bc58b68a3.png)


복구 ( Recover database )
Recover 이후 AUTO 입력

![image](https://user-images.githubusercontent.com/79567212/218965494-2632134f-52bd-403c-b5ee-22f3d4b7d42a.png)

복구 완료 확인
순차적으로 복구 진행 후 복구 완료 확인

![image](https://user-images.githubusercontent.com/79567212/218965537-7f51a75d-2426-4e45-8d55-47c3ed7e4534.png)

DB 오픈 정상 확인
![image](https://user-images.githubusercontent.com/79567212/218965582-90db1b19-2725-485f-b8dc-6a3bf7e559d7.png)

DB Data 정상 확인

DB Full 백업
DB 이상 및 복구 진행 시 반드시 풀 백업 진행

점검표

항목	확인
1. 오라클 구성	컨트롤 파일 다중화 구성
	(1)	컨트롤 파일 DB 설정 조회	　
	(2)	컨트롤 파일 DB 설정 다중화 변경	　
	(3)	컨트롤 파일 변경 적용(물리파일)	　
	아카이브 모드 설정 작업
	(1)	아카이브 모드 현재 상태 조회	　
	(2)	아카이브 모드 설정	　
2. 오라클 백업	오라클 닫힌 백업 
	(1)	DB 정지	　
	(2)	백업 – Datafile, 컨트롤 파일	　
	(3)	백업 – Parameter File	　
	(4)	DB 기동	　
	오라클 열린 백업 
	(1)	아카이브 모드 상태 확인	　
	(2)	백업 – Datafile, 컨트롤 파일	　
	(3)	백업 – Parameter File	　
	(4)	백업 종료	　
	(5)	백업 종료 확인 ( 필수 )	　
3. 오라클 장애 복구
  (컨트롤 파일 장애)	컨트롤 파일 일부 유실(컨트롤 파일 1개 삭제 시, 깨졌을 시, SCN틀어졌을 시)
	(1)	장애확인 1 – 컨트롤 파일 1개 유실	　
	(2)	장애확인 2 – 컨트롤 파일 의 SCN 틀어 졌을 시	　
	(3)	장애 복구	　
	컨트롤 파일 완전 유실
	(1)	장애 확인 ( 컨트롤 파일 장애 로그 )	　
	(2)	컨트롤 파일 기존 위치 확인 및 1차 임시복구	　
	(3)	DB 기동 및 컨트롤 파일 재 생성 스크립트 작성	　
	(4)	DB Shutdown 및 컨트롤 파일 복구	　
	(5)	복구 완료 확인	　
	(6)	DB Full 백업	　
4. 오라클 장애 복구
  (Data File 장애)	Data File 장애 복구
	(1)	장애 확인	　
	(2)	임시 1차 복구	　
	(3)	복구 ( Recover database )	　
	(4)	복구 완료 확인	　
	(5)	DB Full 백업	　













