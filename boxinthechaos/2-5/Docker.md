# Docker
Docker는 애플리케이션을 신속하게 구축, 테스트 및 배포를 할 수 있도록 도와주는 컨테이너 기반 가상화 플렛폼 입니다 전통적인 가상머신(VM)와 다르게 Doker는 호스트 운영체제 위에서 격리된 환경을 만들어 애플리케이션을 실행 시킵니다

## Docker의 주요 개념
1. 이미지(Image): 애플리케이션과 그 실행에 필요한 모든 것을 포함한 불변의 파일입니다 컨테이너를 실행하기 위한 템플릿 역할을 합니다
2. 컨테이너(Container): 이미지를 기반으로 실행되는 인스턴스 입니다 애플리케이션을 격리된 환경에서 실행할 수 있습니다
3. Dockerfile: 이미지를 빌드하기 위한 스크립트 파일로 애플리케이션을 어떻게 구성할지 정의합니다
4. Docker Hub: 이미지를 공유할 수 있는 공식 이미지 저장소 입니다
5. 볼륨(Volume): 컨테이너와 호스트 간 데이터를 영구적으로 저장하고 공유하는 데 사용됩니다
6. 네트워크(Network): 여러 컨테이너를 연결하여 통신할 수 있는 네트워크를 구성합니다

## Docker의 장점
- 경량화: VM과 달리 호스트 커널을 공유하여 메모리와 디스크 공간을 절약합니다
- 빠른 배포: 이미지를 기반으로 하기 때문에 빠르게 컨테이너를 띄울 수 있습니다
- 이식성: 개발 환경과 배포 환경의 불일치를 줄여 어디서나 동일하게 실행됩니다
- 격리성: 애플리케이션 간의 의존성 충돌을 방지합니다

## 도커 명령어 정리
- 이미지 목록 조회: `docker images`
- 컨테이너 목록 조회: `docker ps`
- 컨테이너 로그 보기: `docker logs <container_id>`
- 컨테이너 중지: `docker stop <container_id>`
- 이미지 삭제: `docker rmi <image_id>`
- 모든 컨테이너 삭제: `docker rm $(docker ps - aq)`

# Docker-Compose
Docker-Compose는 여러 개의 Docker 컨테이너를 한 번에 정의하고 관리할 수 있도록 해주는 도구입니다

## Docker-Compose의 특징
1. 멀티 컨테이너 관리: 하나의 파일로 여러 컨테이너를 동시에 관리할 수 있습니다
2. 간단한 명령어로 제어: `docker-compose up`, `docker-compose down`등 간단한 명령으로 모든 컨테이너를 제어할 수 있습니다
3. YAML 파일 사용: 설정 파일인 docker-compose.yml을 사용하여 서비스, 네트워크, 볼륨 등을 정의합니다
4. 네트워크 관리: 본적으로 각 서비스가 같은 네트워크에 연결되어 있어 컨테이너 간 통신이 용이합니다

## Docker-Compose의 기본 구조
```
version: '3.9'
services:
  app:
    image: my-spring-app
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
    depends_on:
      - db

  db:
    image: mariadb:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```

## Docker-Compose 사용법
1. 이미지 빌드 및 컨테이너 실행
```
docker-compose up -d
```
> `-d`옵션은 백그라운드 실행을 의미합니다.
2.  실행 중인 컨테이너 확인
```
docker-compose ps
```
3. 로그 확인
```
docker-compose logs -f
```
4. 컨테이너 중지
```
docker-compose down
```
5. 이미지 및 볼륨까지 삭제
```
docker-compose down --rmi all --volumes
```

## Compose에서 네트워크 연결
Compose는 기본적으로 동일한 프로젝트 내 모든 서비스에 대해 별도의 네트워크를 생성합니다

컨테이너 간에서는 서비스 이름으로 접근이 가능합니다

예를 들자면 Spring Boot 애플리케이션에서 MariaDB에 접근하려면 `jdbc:mysql://db:3306/mydb`로 접근하면 됩니다

## Docker 없이도 로컬 테스트 가능
Docker-Compose를 사용하면 로컬 개발 환경을 완전히 Docker로 구성할 수 있어, 별도의 DB 설치 없이도 Spring Boot 애플리케이션과 MariaDB를 함께 테스트할 수 있습니다