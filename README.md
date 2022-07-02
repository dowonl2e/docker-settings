# docker-settings
Docker에 Spring Boot & MySQL 배포 환경 구성

# 개발 환경 및 기술
- Framework : Spring
- Database : MySQL

# Spring에서 Docker 설정하기
1. 프로젝트 Root 디렉토리 위치에 Dockerfile 생성 및 설정
      
            LABEL title="webserver"
            LABEL version="1.0"
            FROM lpicanco/java11-alpine
            VOLUME /tmp
            ARG JAR_FILE=./build/libs/test-0.0.1.jar
            COPY ${JAR_FILE} app.jar
            ENTRYPOINT ["java", "-jar", "app.jar"]

  - LABEL : 이미지의 버전 정보, 작성자, 코멘트와 같이 이미지 상세 정보를 작성
  - FROM : 새로 생성할 Image 의 기반으로 사용할 image를 지정 (lpicanco/java11-alpine Image를 포함한 image 생성)
  - ARG : Dockerfile build시에 사용하는 변수 
  - ENTRYPOINT : container 실행 시 추가로 실행되어야하는 커멘드 입력 (app.jar 파일 실행)
  
2. Dockerfile로 이미지 빌드하기
  # 설정한 Dockerfile로 이동 후 빌드
  $ docker build -t [이미지명]:[태그명] .
    
3. 프로젝트 Root 디렉토리 위치에 docker-compose.yml 생성 및 설정

      version: "[version]"
    
      services:
            [DB 서비스명]:
                  container_name: "[DB 컨테이너명]"
                  image: [이미지명]:[이미지태그]
                  environment:
                      - MYSQL_ROOT_PASSWORD=[MySQL ROOT 비밀번호]
                      - MYSQL_DATABASE=[DataBase 명]
                      - MYSQL_USER=[계정]
                      - MYSQL_PASSWORD=[비밀번호]
            command:
                  - --character-set-server=utf8mb4
                  - --collation-server=utf8mb4_unicode_ci
            volumes:
                  - ./db/data:/var/lib/mysql:rw
            ports:
                  - [외부접근포트]:[내부포트]

      app:
            build: .
            container_name: [App 컨테이너명]
            ports:
                  - [외부접근포트]:[내부포트]
            depends_on:
                  - [DB 서비스명]
          
4. 프로젝트 연결 DB수정
  - 연결 디비 설정시 docker-compose.yml의 [DB 서비스명]으로 변경
    
      jdbc:mysql://[DB 서비스명]:3306/

5. 프로젝트 빌드하여 jar 파일 생성

6. docker-compose.yml 파일실행
  - docker-compose.yml 파일이 있는곳에서 실행
    $ docker-compose up

# 명령어
백그라운드 실행
  
      $ docker-compose up -d

상태 확인

      $ docker-compose ps

docker-compose 서비스 중단

      $ docker-compose stop

docker-compose 서비스 시작

      $ docker-compose start

docker-compose 서비스 제거 (volume, network 까지 삭제)

      $ docker-compose down

컨테이너 로그 확인

      $ docker-compose logs
  
  
