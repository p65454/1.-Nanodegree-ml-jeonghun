-----------------------------------------------
# 1회차(3월19일 화) VOD 정리
------------------------------------------------

## 1. 구글 클라우드 플랫폼(GCP) 개발 환경 구축

  ### :page_with_curl: 필요한 사전 지식 간단히 정리  
    * 호스트 : 네트워크 연결된 컴퓨터
    * 인스턴스 : 클라우드 서버 컴퓨터
    * 가상머신 : 가상화 한 컴퓨터
    * IP주소 : 네트워크상 주소 (식별가능)
    * 포트번호 : 통신해서 컴퓨터 내 프로그램 찾기위한 번호
    * shell : 터미널
    
  ### :wrench: 사용할 기능
    1. Compute Engine : 서버 컴퓨터 환경 구축
    2. Cloud SQL : 데이터베이스 구축
    3. Cloud Run : 컨테이너 여러개 띄워서 로드를 분산시켜줌. 무중단배포, 로드밸런싱(트래픽을 균등하게 나눠서 배포) 기능
    4. Google Container Registry : 컨테이너 이미지 저장, 관리
    
  * ## **가입 하면 3개월간 300달러 무료(20240319 ~ 2024618)**
  
  ### :exclamation: 인스턴스 만들 때 참고
    * 인스턴스를 만들 때는 가격과 목적을 고려해서 적절히 세팅하자.
    * 리전, 영역 : 서울로 설정(외국보다 속도 빠름)
    * 가용성 정책 (VM 프로비저닝 모델)
      * 표준 : 항상 띄워놓는거
      * 스팟 : 띄워놓지만 리전에 고객사 요청이 많이 들어와서 부족하면 뺏긴다.
               언제든지 이 서버는 내려갈 수 있다.(대신 싸다)
    * 컨테이너 : 인스턴스 띄울 때 자동으로 컨테이너 실행되게 하고 싶을 때 사용
    * 부팅디스크 : 운영체제(ubuntu) / 버전(ubuntu 20.04 LTS) /   
    * 방화벽 : HTTP 트래픽 허용 체크 -> 80포트로 들어오는 요청 허용하겠다 (https는 443포트)
    * 사용 안할 때는 VM인스턴스 창에서 '정지' 상태로 바꿔놓자 크레딧 낭비
    * 브라우저에서 연결하는 법 : VM인스턴스에서 오른쪽 연결에서 SSH 클릭
    
  ### :pushpin: SSH키 등록 (<https://cloud.google.com/compute/docs/connect/create-ssh-keys>)
  요청을 보낼 때 비밀키로 어떤 값을 암호화해서 서버에 전달 한 다음 서버에 등록되어 있는 공개키(.pub)로 복호화 해서 열어주는 원리
    
  -> 공개키를 서버에 등록해야함
  
  1. window 키 등록 명령어 : `ssh-keygen -t rsa -f C:\Users\{사용자명}\.ssh\{파일명} -C {코멘트} -b 2048`
   
  2. 키파일 있는 곳으로 이동 : `cd {키파일 위치}`
   
  3. 퍼블릭 키 출력해서 확인 : `cat {키파일명.pub}`
   
  4. 암호 복사한 다음 Compute Engine - 설정 - 메타데이터 - SSH키 - 항목추가 에 붙여넣기


    
  ### :pushpin: SSH 접속
  1. VM인스턴스 탭에서 외부IP 복사
   
  2. `ssh -i C:/Users/jeonghun/first-ssh-key(공개키의 위치) ubuntu@xxx.xxx.xxx.xxx(외부ip)`
   
  3. 접속이 됐다면 이제 Linux가 된거고 `cd ~/.ssh` -> `cat authorized_keys` 로 등록된 ssh키 확인 가능
----------------------------------------









## 2. 파이썬 환경 세팅

  1. vscode 좌측 원격탐색기 - SSH 옆 + 클릭 - `ssh -i C:/Users/jeonghun/first-ssh-key(공개키의 위치) ubuntu@xxx.xxx.xxx.xxx(외부ip)` - /{홈 경로}/.ssh/config 로 설정

  2. miniconda 설치하기 <https://docs.anaconda.com/free/miniconda/>     
     접속 후 **Linux Miniconda3 Linux 64-bit** 마우스 우클릭 후 링크 복사
     
  3. vs code 에서 'Ctrl + J' 해서 터미널창 킨 후 `wget {링크}` 입력하면 설치된다
     
  4. `bash ./Miniconda3-latest-Linux-x86_64.sh`
      -> bashell 쉘을 켜서 뒤의 프로그램을 실행시키는 명령어 (맨 앞의 .은 현재 위치를 의미)
     
  5. 엔터 치면 라이센스 어쩌고~ 엔터 후 q를 누르면 맨 밑으로 가짐.
      **Do you accept the license terms? [yes|no]** 창이 뜨면 yes
     
  6. 성공 했다면 `/home/ubuntu/miniconda3 >>>` 이런식으로 경로가 홈으로 설정 되있는 모습을 확인 할 수 있다. 엔터
   
  7. **You can undo this by running `conda init --reverse $SHELL? [yes no]**
      -> 아나콘다를 자동으로 활성화 할건지? yes 하면 기본 파이썬이 이번 설치한 아나콘다 환경으로 세팅됨. yes

  8. 껐다 키면 활성화 된다. `exit` 후 `Ctrl + J` 하자
    
  9. mle_course 라는 가상환경 만들기 :  `conda create --name mle_course python=3.11`

  10. 가상환경 활성화 : `conda activate mle_course`
     
  11. Poetry(아나콘다에서 꼬이는 의존성을 해결해주는 툴) 설치 : `conda install poetry`

  12. 프로젝트 생성 : `mkdir ml_basic` -> `cd ml_basic` -> `poetry init` -> `poetry add numpy` 명령어로 라이브러리 설치 가능

  13. vscode 좌측 탐색기 - 폴더열기 - ml_basic 클릭 후 확인

  14. Ctrl + Shift + p 눌러서 파이썬 인터프리터를 방금 만든 mle_course로 선택
