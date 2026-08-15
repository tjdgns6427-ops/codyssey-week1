# Codyssey Week 1 - 개발 워크스테이션 구축

## 1. 프로젝트 개요

본 미션은 터미널, Docker, Git/GitHub를 직접 설정하고 사용하면서 개발 워크스테이션의 기본 구조를 이해하는 것을 목표로 한다.

주요 수행 내용은 다음과 같다.

* 터미널을 이용한 파일 및 디렉터리 조작
* Linux 파일/디렉터리 권한 변경
* Docker 설치 및 기본 운영 명령 확인
* Ubuntu 및 hello-world 컨테이너 실행
* Dockerfile을 이용한 NGINX 웹 서버 이미지 제작
* 포트 매핑을 통한 웹 서버 접속
* Bind Mount를 이용한 호스트 파일 변경 반영
* Docker Volume을 이용한 데이터 영속성 검증
* Git 로컬 저장소 구성 및 GitHub 원격 저장소 연동
* VSCode와 Git/GitHub 연동
* 수행 과정에서 발생한 문제에 대한 트러블슈팅

GitHub Repository:

`https://github.com/tjdgns6427-ops/codyssey-week1`

---

## 2. 실행 환경

| 구분               | 환경                    |
| ---------------- | --------------------- |
| OS               | Windows               |
| Terminal / Shell | Git Bash              |
| Bash             | GNU bash 5.3.15       |
| Docker           | Docker Desktop 29.6.2 |
| Git              | 2.55.0.windows.3      |
| Container OS     | Ubuntu                |
| Web Server       | NGINX Alpine          |
| IDE              | Visual Studio Code    |
| Version Control  | Git / GitHub          |

버전 확인:

```bash
docker --version
git --version
bash --version | head -n 1
```

확인 결과:

```text
Docker version 29.6.2
git version 2.55.0.windows.3
GNU bash, version 5.3.15(1)-release
```

증거: [Git/Bash 버전 확인](evidence/깃버전.png)

---

## 3. 수행 체크리스트

* [x] 터미널 기본 조작 및 파일/폴더 구성
* [x] 파일 생성 및 내용 확인
* [x] 파일 복사, 이동, 이름 변경 및 삭제
* [x] 파일 및 디렉터리 권한 변경 실습
* [x] Docker 설치 및 데몬 동작 확인
* [x] Docker 이미지/컨테이너 목록 확인
* [x] hello-world 컨테이너 실행
* [x] Ubuntu 컨테이너 실행 및 내부 명령 수행
* [x] Docker 로그 확인
* [x] Docker 자원 사용량 확인
* [x] Dockerfile 직접 작성
* [x] 커스텀 Docker 이미지 빌드
* [x] 웹 서버 컨테이너 실행
* [x] 포트 매핑 및 브라우저 접속
* [x] Bind Mount 변경 반영 확인
* [x] Docker Volume 데이터 영속성 확인
* [x] Git 사용자 설정 및 main 브랜치 설정
* [x] Git commit 수행
* [x] GitHub 원격 저장소 연동 및 push
* [x] VSCode에서 Git/GitHub 연동 확인
* [x] 트러블슈팅 2건 수행 및 원인 분석

---

# 4. 터미널 기본 조작

## 4.1 작업 디렉터리 생성 및 확인

```bash
mkdir -p ~/codyssey-week1
cd ~/codyssey-week1

pwd
ls -la
```

`pwd`를 이용하여 현재 작업 위치를 확인하고, `ls -la`를 통해 숨김 파일을 포함한 디렉터리 내용을 확인하였다.

---

## 4.2 파일 및 디렉터리 생성

```bash
mkdir terminal-practice
cd terminal-practice

mkdir testdir
touch test.txt

echo "Codyssey Week 1" > test.txt
cat test.txt
```

출력:

```text
Codyssey Week 1
```

`touch`를 이용하여 빈 파일을 생성하고, `echo`와 출력 리다이렉션(`>`)을 이용해 파일 내용을 작성하였다.

`cat`을 통해 작성된 파일 내용을 확인하였다.

---

## 4.3 파일 복사 및 이름 변경

```bash
cp test.txt copy.txt
mv copy.txt renamed.txt
ls -la
```

`cp`를 이용해 파일을 복사한 뒤 `mv`를 이용하여 복사된 파일의 이름을 변경하였다.

---

## 4.4 파일 삭제

```bash
touch delete-me.txt
ls -la

rm delete-me.txt
ls -la
```

삭제 전 파일의 존재를 확인하고 `rm` 실행 후 파일이 목록에서 사라진 것을 확인하였다.

### 증거 자료

* [터미널 기본 조작 1](evidence/터미널1.png)
* [터미널 기본 조작 2](evidence/터미널2.png)
* [터미널 기본 조작 3 / 권한 실습](evidence/터미널3.png)

---

# 5. 파일 및 디렉터리 권한 실습

Windows Git Bash에서 다음 명령을 실행하였다.

```bash
ls -l test.txt
chmod 755 test.txt
ls -l test.txt
```

그러나 Windows NTFS 파일 시스템에서는 Linux와 동일한 형태의 권한 변경이 정상적으로 반영되지 않았다.

따라서 실제 Linux 환경인 Ubuntu Docker 컨테이너에서 동일한 실습을 수행하였다.

```bash
docker run -it --name permission-test ubuntu bash

mkdir testdir
touch test.txt

ls -l test.txt
chmod 755 test.txt
ls -l test.txt

ls -ld testdir
chmod 700 testdir
ls -ld testdir
```

파일 권한:

```text
-rw-r--r--
```

에서

```text
-rwxr-xr-x
```

로 변경된 것을 확인하였다.

디렉터리 권한:

```text
drwxr-xr-x
```

에서

```text
drwx------
```

로 변경된 것을 확인하였다.

### 권한 숫자의 의미

`755`는 다음과 같이 해석할 수 있다.

```text
7 = 4(read) + 2(write) + 1(execute)
5 = 4(read) + 1(execute)
5 = 4(read) + 1(execute)
```

따라서 `755`는 다음 권한을 의미한다.

```text
소유자: rwx
그룹:   r-x
기타:   r-x
```

`644`는 다음과 같다.

```text
소유자: rw-
그룹:   r--
기타:   r--
```

---

# 6. Docker 설치 및 기본 점검

Docker 버전을 확인하였다.

```bash
docker --version
```

Docker 데몬 및 실행 환경을 확인하였다.

```bash
docker info
```

이미지 목록:

```bash
docker images
```

전체 컨테이너 목록:

```bash
docker ps -a
```

Docker Desktop의 Client와 Server가 정상적으로 연결되어 있으며 Docker 엔진이 정상 실행 중인 것을 확인하였다.

### 증거 자료

* [Docker 설치 및 정보 확인 1](evidence/도커1.png)
* [Docker 설치 및 정보 확인 2](evidence/도커2.png)
* [Docker 이미지/컨테이너 확인](evidence/도커3.png)

---

# 7. Docker 기본 운영 및 컨테이너 실행

## 7.1 hello-world 실행

```bash
docker run hello-world
```

정상 출력:

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

Docker Client → Docker Daemon → Image → Container의 기본 실행 과정이 정상적으로 동작함을 확인하였다.

---

## 7.2 Ubuntu 컨테이너 실행

```bash
docker run -dit --name week1-ubuntu ubuntu sleep infinity
docker ps
```

실행 중인 컨테이너 내부에 다음 명령으로 진입하였다.

```bash
docker exec -it week1-ubuntu bash
```

컨테이너 내부에서:

```bash
ls
echo "Hello from Ubuntu container"
pwd
```

출력:

```text
Hello from Ubuntu container
/
```

컨테이너를 종료하였다.

```bash
docker stop week1-ubuntu
docker ps -a
```

### `run`, `exec`, `attach`의 차이

* `docker run`: 새로운 컨테이너를 생성하고 실행한다.
* `docker exec`: 이미 실행 중인 컨테이너 내부에서 새로운 명령이나 셸을 실행한다.
* `docker attach`: 실행 중인 컨테이너의 기존 메인 프로세스에 직접 연결한다.

이번 실습에서는 실행 중인 컨테이너를 유지한 채 내부를 확인하기 위해 `docker exec`를 사용하였다.

---

## 7.3 Docker 자원 사용량 확인

```bash
docker stats --no-stream
```

CPU, 메모리, 네트워크 및 I/O 사용량을 확인하였다.

---

## 7.4 Docker 로그 확인

테스트 로그를 출력하는 컨테이너를 생성하였다.

```bash
docker run --name week1-log ubuntu bash -c 'echo "Docker log test successful"'
docker logs week1-log
```

출력:

```text
Docker log test successful
```

명령 입력 과정에서 한 차례 명령어를 중복 입력하여 오류가 발생하였으나, 명령을 올바르게 다시 입력한 후 정상적으로 로그가 출력되는 것을 확인하였다.

### 증거 자료

- [컨테이너 실행실습1](evidence/컨테이너%20실행실습1.png)
- [컨테이너 실행실습2](evidence/컨테이너%20실행실습2.png)
- [컨테이너 실행실습3](evidence/컨테이너%20실행실습3.png)
- [컨테이너 실행실습4](evidence/컨테이너%20실행실습4.png)

---

# 8. Dockerfile 기반 웹 서버 이미지 제작

이번 실습에서는 **NGINX Alpine 기반 이미지**를 사용하였다.

NGINX는 정적 웹 콘텐츠를 제공하기 적합하며, Alpine 기반 이미지는 상대적으로 크기가 작아 간단한 웹 서버 실습에 적합하기 때문에 선택하였다.

## 8.1 웹 페이지 생성

```bash
echo '<h1>Codyssey Week 1 Docker Web Server</h1>' > index.html
cat index.html
```

---

## 8.2 Dockerfile 작성

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

### Dockerfile 목적

* `FROM nginx:alpine`

  * NGINX가 설치된 Alpine Linux 기반 이미지를 Base Image로 사용한다.

* `COPY index.html /usr/share/nginx/html/index.html`

  * 로컬의 `index.html`을 NGINX 기본 정적 웹 콘텐츠 경로로 복사한다.

---

## 8.3 이미지 빌드

```bash
docker build -t week1-web:1.0 .
```

빌드 결과를 확인하였다.

```bash
docker images week1-web
```

`week1-web:1.0` 이미지가 정상 생성된 것을 확인하였다.

### 증거 자료

* [웹 서버 Dockerfile 및 Build](evidence/웹서버1.png)
* [웹 서버 이미지 확인](evidence/웹서버2.png)

---

# 9. 포트 매핑 및 웹 서버 접속

웹 서버 컨테이너를 다음과 같이 실행하였다.

```bash
docker run -d -p 8080:80 \
  --name week1-web-container \
  week1-web:1.0
```

컨테이너 상태를 확인하였다.

```bash
docker ps
```

포트 정보:

```text
0.0.0.0:8080->80/tcp
```

이는 호스트 PC의 `8080` 포트로 들어온 요청을 컨테이너의 NGINX `80` 포트로 전달한다는 의미이다.

브라우저에서 다음 주소로 접속하였다.

```text
http://localhost:8080
```

웹페이지:

```text
Codyssey Week 1 Docker Web Server
```

가 정상적으로 출력되는 것을 확인하였다.

### 포트 매핑이 필요한 이유

Docker 컨테이너는 호스트와 격리된 네트워크 환경에서 실행된다.

따라서 컨테이너 내부의 웹 서버 포트만 열어 두어서는 호스트 브라우저에서 바로 접근할 수 없으며, `-p` 옵션을 사용해 호스트 포트와 컨테이너 포트를 연결해야 한다.

### 증거

[localhost:8080 웹 서버 접속](evidence/웹서버3.png)

---

# 10. Bind Mount 검증

호스트 PC의 파일 변경 내용이 실행 중인 컨테이너에 즉시 반영되는지 확인하였다.

Windows Git Bash의 경로 자동 변환 문제를 피하기 위해 별도의 실습 디렉터리를 생성하였다.

```bash
mkdir /c/codyssey-bind
cd /c/codyssey-bind

echo '<h1>Bind Mount BEFORE</h1>' > index.html
cat index.html
```

컨테이너 실행:

```bash
MSYS_NO_PATHCONV=1 docker run -d \
  -p 8081:80 \
  --name week1-bind \
  --mount "type=bind,source=C:\codyssey-bind,target=/usr/share/nginx/html,readonly" \
  nginx:alpine
```

브라우저:

```text
http://localhost:8081
```

출력:

```text
Bind Mount BEFORE
```

호스트 파일을 변경하였다.

```bash
echo '<h1>Bind Mount AFTER - Host File Changed</h1>' > index.html
cat index.html
```

컨테이너를 다시 생성하거나 이미지를 다시 빌드하지 않고 브라우저를 새로고침하였다.

출력:

```text
Bind Mount AFTER - Host File Changed
```

즉, 호스트의 디렉터리와 컨테이너의 `/usr/share/nginx/html`이 직접 연결되어 있기 때문에 호스트의 파일 변경 내용이 컨테이너에 즉시 반영됨을 확인하였다.

### 증거 자료

* [Bind Mount 설정](evidence/마운트변경1.png)
* [Bind Mount BEFORE](evidence/마운트변경2.png)
* [호스트 파일 변경](evidence/마운트변경3.png)
* [Bind Mount AFTER](evidence/마운트변경4.png)

---

# 11. Docker Volume 영속성 검증

Docker Volume을 생성하였다.

```bash
docker volume create week1-data
docker volume ls
```

첫 번째 컨테이너에 볼륨을 연결하였다.

```bash
MSYS_NO_PATHCONV=1 docker run -d \
  --name week1-vol-1 \
  -v week1-data:/data \
  ubuntu sleep infinity
```

볼륨에 데이터를 저장하였다.

```bash
MSYS_NO_PATHCONV=1 docker exec week1-vol-1 \
  bash -c 'echo "Codyssey persistent data" > /data/hello.txt && cat /data/hello.txt'
```

출력:

```text
Codyssey persistent data
```

첫 번째 컨테이너를 삭제하였다.

```bash
docker rm -f week1-vol-1
```

동일한 Volume을 두 번째 컨테이너에 연결하였다.

```bash
MSYS_NO_PATHCONV=1 docker run -d \
  --name week1-vol-2 \
  -v week1-data:/data \
  ubuntu sleep infinity
```

기존 데이터를 확인하였다.

```bash
MSYS_NO_PATHCONV=1 docker exec week1-vol-2 cat /data/hello.txt
```

출력:

```text
Codyssey persistent data
```

첫 번째 컨테이너가 삭제되었음에도 데이터가 유지되었다.

이를 통해 **컨테이너의 생명주기와 Docker Volume의 데이터 생명주기가 분리되어 있음**을 확인하였다.

### 증거

[Docker Volume 영속성 검증](evidence/볼륨영속성1.png)

---

# 12. Git 설정 및 GitHub 연동

## 12.1 Git 저장소 설정

```bash
cd ~/codyssey-week1

git init

git config user.name "tjdgns6427"
git config user.email "<GitHub 계정 이메일>"

git branch -M main
```

설정 확인:

```bash
git config --list
git branch --show-current
git status
```

현재 브랜치가 `main`임을 확인하였다.

---

## 12.2 Commit

```bash
git add .
git status

git commit -m "Complete week1 development environment assignment"
```

정상적으로 첫 커밋이 생성되었다.

### 증거

* [Git 설정](evidence/깃설정1.png)
* [Git Commit](evidence/깃설정2.png)

---

## 12.3 GitHub 원격 저장소 연동

원격 저장소를 설정하였다.

```bash
git remote set-url origin https://github.com/tjdgns6427-ops/codyssey-week1.git
```

확인:

```bash
git remote -v
```

GitHub와 로컬 저장소 동기화 상태:

```bash
git status
git log -1 --oneline
```

최종적으로:

```text
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

상태를 확인하였다.

### 증거

* [Git 원격 저장소 연동](evidence/깃1.png)
* [GitHub Repository](evidence/깃2.png)

---

# 13. VSCode와 GitHub 연동

Visual Studio Code에서 `codyssey-week1` 프로젝트를 열고 Source Control 기능을 이용하여 Git 저장소가 정상적으로 인식되는지 확인하였다.

확인 사항:

* GitHub 계정 `tjdgns6427-ops` 로그인
* 프로젝트 Git 저장소 인식
* Source Control 활성화
* `main` 브랜치 확인
* 기존 Commit Graph 확인

### 증거

* [VSCode 프로젝트 및 GitHub 로그인](evidence/깃vs코드1.png)
* [VSCode Source Control / main 브랜치](evidence/깃vs코드2.png)

---

# 14. 트러블슈팅

## 14.1 Windows Git Bash에서 chmod 권한 변경 미반영

### 문제

Windows Git Bash에서 다음 명령으로 파일 권한을 변경하였다.

```bash
touch chmod-trouble.txt

ls -l chmod-trouble.txt
chmod 755 chmod-trouble.txt
ls -l chmod-trouble.txt
```

그러나 변경 전후 모두:

```text
-rw-r--r--
```

로 표시되어 `chmod 755`가 반영되지 않았다.

### 원인 / 가설

Git Bash는 Linux와 유사한 명령 환경을 제공하지만 실제 파일은 Windows의 NTFS 파일 시스템에 존재한다.

따라서 Linux의 POSIX 권한 체계가 Windows 파일 시스템에 동일하게 적용되지 않아 `chmod` 결과가 정상적으로 반영되지 않는 것으로 판단하였다.

### 확인

실제 Linux 환경인 Ubuntu Docker 컨테이너에서 동일한 실험을 수행하였다.

```bash
docker run --rm -it ubuntu bash

touch chmod-trouble.txt
ls -l chmod-trouble.txt

chmod 755 chmod-trouble.txt
ls -l chmod-trouble.txt
```

결과:

```text
변경 전
-rw-r--r--

변경 후
-rwxr-xr-x
```

### 해결 / 대안

Linux 파일 권한을 검증해야 하는 실습은 Windows Git Bash가 아닌 Ubuntu Docker 컨테이너 환경에서 수행하였다.

이를 통해 실제 Linux 파일 시스템에서 `chmod`가 정상적으로 동작하는 것을 확인하였다.

### 증거

[트러블슈팅 1 - chmod](evidence/트러블슈팅1.png)

---

## 14.2 Docker 포트 충돌

### 문제

새 NGINX 컨테이너를 다음과 같이 실행하려 하였다.

```bash
docker run -d -p 8080:80 \
  --name port-trouble \
  nginx:alpine
```

다음 오류가 발생하였다.

```text
Bind for 0.0.0.0:8080 failed:
port is already allocated
```

### 원인 / 가설

호스트 PC의 `8080` 포트를 이미 다른 Docker 컨테이너가 사용하고 있기 때문에 새 컨테이너가 같은 포트를 사용할 수 없는 것으로 판단하였다.

### 확인

```bash
docker ps
```

실행 결과 기존 `week1-web-container`가 다음 포트를 사용 중인 것을 확인하였다.

```text
0.0.0.0:8080->80/tcp
```

따라서 포트 충돌이 원인임을 확인하였다.

### 해결 / 대안

충돌한 컨테이너을 제거한 후 새로운 호스트 포트 `8083`을 할당하였다.

```bash
docker rm -f port-trouble

docker run -d -p 8083:80 \
  --name port-trouble \
  nginx:alpine

docker ps
```

결과:

```text
0.0.0.0:8083->80/tcp
```

새 컨테이너가 정상적으로 실행되었다.

### 해결 방법 정리

동일한 호스트 포트를 여러 컨테이너가 동시에 사용할 수 없으므로 다음 방법 중 하나를 사용할 수 있다.

1. 기존에 해당 포트를 사용하는 컨테이너를 종료한다.
2. 새로운 컨테이너에 다른 호스트 포트를 할당한다.

이번 실습에서는 두 번째 방법을 사용하였다.

### 증거

[트러블슈팅 2 - Docker 포트 충돌](evidence/트러블슈팅2.png)

---

# 15. Bind Mount와 Docker Volume 차이

## Bind Mount

호스트 PC의 실제 디렉터리를 컨테이너 내부 디렉터리에 직접 연결한다.

```text
Host Directory
      │
      ▼
Docker Container
```

특징:

* 호스트 파일을 직접 수정할 수 있다.
* 변경 내용이 컨테이너에 즉시 반영된다.
* 개발 환경에서 소스코드를 연결할 때 유용하다.
* 호스트의 경로 구조에 의존한다.

이번 실습에서는 `index.html`을 변경하여 웹페이지가 즉시 변경되는 것을 확인하였다.

## Docker Volume

Docker가 자체적으로 관리하는 데이터 저장 영역이다.

```text
Docker Volume
      │
      ├── Container A
      │
      └── Container B
```

특징:

* 특정 컨테이너와 독립적으로 데이터를 보관한다.
* 컨테이너를 삭제해도 데이터가 유지될 수 있다.
* 데이터베이스 등 영속적인 데이터 저장에 적합하다.

이번 실습에서는 첫 번째 컨테이너를 삭제한 뒤 새로운 컨테이너에서 동일한 데이터를 읽어 영속성을 확인하였다.

---

# 16. Git과 GitHub의 차이

## Git

Git은 로컬 컴퓨터에서 소스코드의 변경 이력을 관리하는 **분산 버전 관리 시스템**이다.

주요 기능:

* 변경 이력 관리
* Commit
* Branch
* Merge
* 이전 버전 복구

## GitHub

GitHub는 Git 저장소를 인터넷을 통해 저장하고 공유할 수 있도록 제공하는 **원격 협업 플랫폼**이다.

주요 기능:

* 원격 Repository
* 팀원 간 코드 공유
* Pull Request
* Issue 관리
* 코드 리뷰 및 협업

정리하면 다음과 같다.

```text
Git
→ 로컬에서 버전을 관리하는 도구

GitHub
→ Git 저장소를 원격에서 공유하고 협업하기 위한 플랫폼
```

---

# 17. 주요 검증 결과

| 항목           | 검증 방법                                           | 결과             |
| ------------ | ----------------------------------------------- | -------------- |
| 터미널 기본 명령    | `pwd`, `ls`, `mkdir`, `touch`, `cp`, `mv`, `rm` | 정상             |
| 파일 내용        | `echo`, `cat`                                   | 정상             |
| 파일 권한        | `chmod`, `ls -l`                                | Ubuntu에서 정상 변경 |
| Docker 설치    | `docker --version`                              | 정상             |
| Docker 데몬    | `docker info`                                   | 정상             |
| 이미지          | `docker images`                                 | 정상             |
| 컨테이너         | `docker ps`, `docker ps -a`                     | 정상             |
| 로그           | `docker logs`                                   | 정상             |
| 자원 상태        | `docker stats`                                  | 정상             |
| hello-world  | `docker run hello-world`                        | 정상             |
| Ubuntu       | `docker exec`                                   | 정상             |
| Docker Build | `docker build`                                  | 정상             |
| 포트 매핑        | `8080:80`                                       | 정상             |
| 웹 접속         | `localhost:8080`                                | 정상             |
| Bind Mount   | BEFORE → AFTER                                  | 정상 반영          |
| Volume       | 컨테이너 삭제 후 파일 확인                                 | 데이터 유지         |
| Git          | `git status`, `git log`                         | 정상             |
| GitHub       | `git remote -v`                                 | 정상             |
| VSCode       | Source Control                                  | 정상             |

---

# 18. 증거 자료 전체 목록

모든 수행 결과는 `evidence` 디렉터리에 저장하였다.

## 터미널

* [터미널1](evidence/터미널1.png)
* [터미널2](evidence/터미널2.png)
* [터미널3](evidence/터미널3.png)

## Docker

* [도커1](evidence/도커1.png)
* [도커2](evidence/도커2.png)
* [도커3](evidence/도커3.png)

## 컨테이너 실행

- [컨테이너 실행실습1](evidence/컨테이너%20실행실습1.png)
- [컨테이너 실행실습2](evidence/컨테이너%20실행실습2.png)
- [컨테이너 실행실습3](evidence/컨테이너%20실행실습3.png)
- [컨테이너 실행실습4](evidence/컨테이너%20실행실습4.png)

## Dockerfile / 웹 서버

* [웹서버1](evidence/웹서버1.png)
* [웹서버2](evidence/웹서버2.png)
* [웹서버3](evidence/웹서버3.png)

## Bind Mount

* [마운트변경1](evidence/마운트변경1.png)
* [마운트변경2](evidence/마운트변경2.png)
* [마운트변경3](evidence/마운트변경3.png)
* [마운트변경4](evidence/마운트변경4.png)

## Docker Volume

* [볼륨영속성1](evidence/볼륨영속성1.png)

## Git / GitHub

* [Git 설정1](evidence/깃설정1.png)
* [Git 설정2](evidence/깃설정2.png)
* [Git 원격 연동](evidence/깃1.png)
* [GitHub Repository](evidence/깃2.png)
* [Git/Bash 버전](evidence/깃버전.png)
-[Git 원격 연동](evidence/Git%20원격%20연동.png)
## VSCode

* [VSCode / GitHub 1](evidence/깃vs코드1.png)
* [VSCode / GitHub 2](evidence/깃vs코드2.png)

## 트러블슈팅

* [chmod 권한 문제](evidence/트러블슈팅1.png)
* [Docker 포트 충돌](evidence/트러블슈팅2.png)

---

# 19. 재현 방법

Repository를 clone한다.

```bash
git clone https://github.com/tjdgns6427-ops/codyssey-week1.git
cd codyssey-week1
```

Docker 이미지를 빌드한다.

```bash
docker build -t week1-web:1.0 .
```

컨테이너를 실행한다.

```bash
docker run -d \
  -p 8080:80 \
  --name week1-web-container \
  week1-web:1.0
```

실행 상태 확인:

```bash
docker ps
```

브라우저에서 다음 주소로 접속한다.

```text
http://localhost:8080
```

다음 내용이 표시되면 정상이다.

```text
Codyssey Week 1 Docker Web Server
```

컨테이너 종료 및 제거:

```bash
docker stop week1-web-container
docker rm week1-web-container
```

---

# 20. 보안 및 개인정보 보호

* GitHub Token, Password 등 인증 정보는 Repository에 저장하지 않았다.
* 터미널 캡처에 인증 토큰이나 비밀번호가 포함되지 않도록 확인하였다.
* Git 사용자 설정에는 버전 관리에 필요한 사용자 정보만 설정하였다.
* 향후 환경변수나 인증 정보가 필요한 경우 `.env` 파일 등을 사용할 경우 `.gitignore`를 통해 Repository에 업로드되지 않도록 관리해야 한다.

---

# 21. 최종 정리

이번 미션을 통해 개발 환경이 단순히 코드를 작성하는 공간이 아니라 터미널, 파일 시스템, Docker, 네트워크, 스토리지 및 Git/GitHub가 연결된 하나의 작업 환경이라는 것을 확인하였다.

특히 Docker 실습을 통해 다음 구조를 직접 검증하였다.

```text
Docker Image
     │
     ▼
Container
     │
     ├── Port Mapping → 외부 접속
     │
     ├── Bind Mount → 호스트 파일 변경 반영
     │
     └── Volume → 영속적인 데이터 저장
```

또한 Git을 이용하여 로컬에서 버전을 관리하고 GitHub 원격 저장소와 연결함으로써 로컬 개발 환경과 원격 협업 환경을 구성하였다.

트러블슈팅 과정에서는 Windows와 Linux의 파일 권한 체계 차이 및 Docker 포트 충돌 문제를 직접 확인하고 원인을 분석한 뒤 대안을 적용하였다.

이를 통해 동일한 개발 환경을 다시 구성하고 문제 발생 시 명령어와 실행 결과를 기반으로 원인을 확인하는 기본적인 개발 워크스테이션 운영 과정을 수행하였다.
