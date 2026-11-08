# E1-1 개발 워크스테이션 구축

> 진행 중인 과제 기록입니다. 확인되지 않은 실습은 완료로 표시하지 않았습니다. 실제 명령과 출력, 캡처 링크를 추가하면서 갱신하세요.

## 미션 개요

터미널과 파일 권한을 다루고 Docker로 웹 서버를 컨테이너화합니다. 포트 매핑, 바인드 마운트 변경 반영, 볼륨 데이터 영속성을 검증하고 수행 결과를 문서화해 GitHub에 제출합니다.

## 실행 환경

| 항목 | 확인값 |
|---|---|
| 작업 폴더 | `C:\Users\ADMIN\E1-1` |
| OS | Windows 11 Home 25H2 (OS 빌드 26200.9711) |
| 쉘 | PowerShell 7.6.9 |
| Docker CLI | Docker version 29.9.2, build a82e528 |
| Docker 데몬 | Docker Server 29.9.2; 컨테이너 5개(실행 3, 중지 2), 이미지 4개 |
| Git | git version 2.56.0.windows.1 |
| GitHub | `https://github.com/smz7093-web/E1-1` |

> 참고: 빌드 26200은 Windows 11입니다. `Get-ComputerInfo`와 레지스트리 `ProductName`은 호환성 때문에 "Windows 10"으로 표시되므로 빌드 번호(`winver`)로 확인했습니다.

```powershell
$PSVersionTable.PSVersion
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion' | Select-Object DisplayVersion, CurrentBuild, UBR
docker --version
docker info
git --version
```

`docker info` 출력에 민감한 정보가 있으면 공유 전에 가립니다.

## 수행 체크리스트

- [x] Linux 터미널에서 위치 이동, 파일/디렉터리 생성 및 목록 확인 (캡처 포함)
- [x] 터미널 파일 내용/빈 파일, 복사·이름 변경·삭제 실습 캡처
- [x] 파일 및 디렉터리 권한 변경 전후 캡처
- [x] `hello-world`, Ubuntu 컨테이너 실행/재진입 실습 캡처
- [x] Docker CLI 버전 확인 (`29.9.2`)
- [x] `docker info` 핵심 출력 요약 기록
- [x] 이미지 목록, 로그·리소스 점검 결과 기록
- [x] 컨테이너 목록 확인 (`docker ps`, 캡처 포함)
- [x] 컨테이너 로그 및 리소스 확인 (`docker logs`, `docker stats`)
- [x] 직접 작성한 Dockerfile 빌드 및 실행
- [x] 포트 매핑 접속 증거(브라우저 주소창/응답)
- [x] 바인드 마운트 호스트 변경 응답 확인
- [ ] 컨테이너 삭제 전후 Docker 볼륨 데이터 확인
- [x] GitHub 저장소 생성 및 README 푸시
- [ ] `git config --list` 결과와 VS Code GitHub 로그인/저장소 연동 증거
- [x] 트러블슈팅 2건 기록

## 실습 캡처

아래 캡처는 `captures/` 폴더에 원본을 보관했습니다.

| 캡처 | 확인되는 내용 |
|---|---|
| [mydir_list.jpg](captures/mydir_list.jpg) | Ubuntu 컨테이너 `/temp`에서 `touch`, `mkdir`, `ls -l`로 파일과 디렉터리 생성/목록 확인 |
| [chmod.jpg](captures/chmod.jpg) | `file.txt`에 `chmod 600`, `chmod 700`을 적용하고 `ls -l`로 권한 확인. `cdmod` 오타 오류도 기록되어 있음 |
| [cp_rm_mv.jpg](captures/cp_rm_mv.jpg) | `/tmp/e1-practice`에서 빈 파일 생성, 파일 내용 확인, `cp`, `mv` 결과 확인 |
| [cp_rm_mv_chmod.jpg](captures/cp_rm_mv_chmod.jpg) | `rm` 및 삭제 후 목록 확인, 디렉터리에 `chmod 700` 적용 후 `ls -ld`로 확인 |
| [docker_run.jpg](captures/docker_run.jpg) | PowerShell에서 `docker run hello-world` 성공 출력 |
| [exit_exec.jpg](captures/exit_exec.jpg) | 종료된 Ubuntu 컨테이너 시작 후 `docker exec` 재진입, 파일/디렉터리 확인, `exit` 뒤 컨테이너가 실행 중임을 `docker ps`로 확인 |
| [linux_os_release.jpg](captures/linux_os_release.jpg) | 컨테이너 안에서 `/etc/os-release` 확인; 출력은 Ubuntu 26.04.1 LTS |
| [localhost.jpg](captures/localhost.jpg) | 브라우저 `localhost:8080`에서 Docker 웹 페이지 응답 확인 |
| [host_change.jpg](captures/host_change.jpg) | 브라우저 `localhost:8081`에서 호스트 수정 후 문구가 반영된 웹 페이지 확인 |

볼륨 영속성과 VS Code 연동 증거는 아직 없습니다. 바인드 마운트는 변경 후 브라우저 응답 캡처를 확보했으며, 변경 전후 터미널 응답 로그도 추가하면 증거가 더 분명해집니다.

## 터미널 조작 기록

터미널 실습은 Ubuntu 컨테이너의 Bash에서 수행했습니다. 수행한 명령의 흐름은 다음과 같습니다(실제 출력은 캡처 참고).

```bash
pwd
mkdir -p /tmp/e1-practice && cd /tmp/e1-practice
touch empty.txt
echo "practice_text" > note.txt
cat note.txt
cp note.txt copy.txt
mv copy.txt renamed.txt
rm renamed.txt
ls -l
```

| 목적 | 명령 및 실제 출력 |
|---|---|
| 위치/목록 | [mydir_list.jpg](captures/mydir_list.jpg), [cp_rm_mv.jpg](captures/cp_rm_mv.jpg) |
| 파일·폴더 조작 | [cp_rm_mv.jpg](captures/cp_rm_mv.jpg), [cp_rm_mv_chmod.jpg](captures/cp_rm_mv_chmod.jpg) |
| 파일 내용/빈 파일 | `cat note.txt` 출력 및 0 byte `empty.txt`가 캡처에 보임 |

관련 캡처: [mydir_list.jpg](captures/mydir_list.jpg)

## 권한 실습

권한 실습은 Ubuntu 컨테이너에서 Linux `chmod`로 수행했습니다. (Windows ACL은 별도 모델이라 다루지 않았습니다.)

```bash
ls -l file.txt
chmod 600 file.txt && ls -l file.txt
chmod 700 file.txt && ls -l file.txt
ls -ld .
chmod 700 . && ls -ld .
```

| 대상 | 변경 전 | 수행한 변경 | 변경 후 |
|---|---|---|---|
| 파일 `file.txt` | `-rw-r--r--` | `chmod 600`, 이어 `chmod 700` | 각각 `-rw-------`, `-rwx------` |
| 디렉터리 `e1-practice` | `drwxr-xr-x` | `chmod 700 .` | `drwx------` |

파일 권한 캡처: [chmod.jpg](captures/chmod.jpg). 파일 및 디렉터리 관리 결과: [cp_rm_mv_chmod.jpg](captures/cp_rm_mv_chmod.jpg).

## Docker 점검 및 기본 운영

`docker --version`과 `docker info` 확인 결과의 핵심 항목을 요약했습니다. 상세 정보에서 고유 ID, 프록시 및 내부 레지스트리 주소는 제외했습니다. [docker_info.txt](docker_info.txt)

```text
> docker --version
Docker version 29.9.2, build a82e528

> docker info
Client: 29.9.2
Server: 29.9.2
Containers: 5 (Running: 3, Stopped: 2)
Images: 4
Environment: Docker Desktop, Linux containers on WSL2
```

`hello-world` 실행 증거: [docker_run.jpg](captures/docker_run.jpg)

```powershell
docker images
docker ps
docker ps -a
docker logs <컨테이너명>
docker stats --no-stream
docker run --rm hello-world
```

컨테이너 목록 증거는 [exit_exec.jpg](captures/exit_exec.jpg)에 있습니다. 출력이 길어 캡처 대신 텍스트로 저장했습니다.

| 명령 | 전체 출력 | 요약 |
|---|---|---|
| `docker images` | [logs/docker_images.txt](logs/docker_images.txt) | `e1-web:1.0`(93.6MB), `nginx:alpine`, `ubuntu:latest`, `hello-world:latest` 4개 |
| `docker logs e1-web` | [logs/docker_logs_e1-web.txt](logs/docker_logs_e1-web.txt) | nginx 1.31.8 시작, `GET /` 200 응답(curl, Chrome), `favicon.ico` 404 |
| `docker stats --no-stream` | [logs/docker_stats.txt](logs/docker_stats.txt) | `e1-bind`, `e1-web`, `mybox` 실행 중. CPU 0%, 메모리 각 12~16MiB |

### Ubuntu 컨테이너

```powershell
docker run -it --name mybox ubuntu bash
# 컨테이너 안에서
pwd
ls -la
echo "hello from ubuntu"
exit
```

`docker run -it`는 컨테이너를 새로 만들고 대화형으로 진입하며, `docker exec -it`는 실행 중인 컨테이너에 추가 프로세스로 진입합니다. 종료 후 상태와 재진입을 관찰해 기록합니다.

```powershell
docker ps -a
docker start mybox
docker exec -it mybox bash
```

관찰: `docker ps -a`에서 `mybox`가 종료된 상태인 것을 확인하고 `docker start mybox`로 재시작했습니다. `docker exec -it mybox bash`로 진입해 명령을 실행한 뒤 `exit`해도 원래 컨테이너는 실행 중이어서 `docker ps`에 표시됐습니다.

관련 캡처: [exit_exec.jpg](captures/exit_exec.jpg), [linux_os_release.jpg](captures/linux_os_release.jpg)

## Dockerfile 웹 서버

- 베이스 이미지: `nginx:alpine` (경량 NGINX 웹 서버)
- 커스텀 내용: 직접 만든 정적 HTML을 이미지의 기본 웹 루트로 복사
- 웹 소스: [`site/index.html`](site/index.html)

```text
E1-1/
├── README.md
├── Dockerfile
└── site/index.html
```

실제 Dockerfile과 소스는 저장소에 포함하고 이 문서에서 링크합니다.

```dockerfile
FROM nginx:alpine
COPY site/ /usr/share/nginx/html/
```

```powershell
docker build -t e1-web:1.0 .
docker run -d --name e1-web -p 8080:80 e1-web:1.0
docker ps
curl.exe http://localhost:8080
docker logs e1-web
```

- 접속 주소: `http://localhost:8080`
- 브라우저 접속 증거: [localhost.jpg](captures/localhost.jpg)

## 바인드 마운트 반영

호스트 파일을 연결하고 수정 전후 응답을 비교합니다. 경로는 실제 구성에 맞게 바꿉니다.

```powershell
docker run -d --name e1-bind -p 8081:80 -v "${PWD}\site:/usr/share/nginx/html:ro" nginx:alpine
curl.exe http://localhost:8081
# site/index.html을 수정한 뒤 다시 요청
curl.exe http://localhost:8081
```

- 변경 결과: 호스트 파일의 문구 `호스트 변경 반영 확인`이 컨테이너 웹 응답으로 표시됨
- 브라우저 증거: [host_change.jpg](captures/host_change.jpg)
- 변경 전후 동일 포트의 터미널 응답 로그: 추가 기록 필요

## Docker 볼륨 영속성

```powershell
docker volume create e1-data
docker run -d --name e1-vol -v e1-data:/data ubuntu sleep infinity
docker exec e1-vol bash -lc "echo persistent-data > /data/check.txt && cat /data/check.txt"
docker rm -f e1-vol
docker run -d --name e1-vol-check -v e1-data:/data ubuntu sleep infinity
docker exec e1-vol-check cat /data/check.txt
docker volume inspect e1-data
docker rm -f e1-vol-check
```

- 삭제 전 데이터: 기록 필요
- 삭제 후 새 컨테이너에서 읽은 값: 기록 필요
- 볼륨 검사 결과: 기록 필요

## Git 및 GitHub

```powershell
git config --global user.name "<표시 이름>"
git config --global user.email "<GitHub 등록 이메일>"
git config --global init.defaultBranch main
git config --list
git init
git add README.md
git commit -m "docs: add E1-1 mission README"
git branch -M main
git remote add origin https://github.com/smz7093-web/E1-1.git
git push -u origin main
```

- 저장소: [smz7093-web/E1-1](https://github.com/smz7093-web/E1-1) (README 푸시 완료)
- VS Code GitHub 로그인/저장소 연동 캡처: 추가 필요
- 민감정보 점검: 완료 여부 기록(토큰·비밀번호는 문서화하지 않음)

## 트러블슈팅

### 사례 1

- 문제: `cdmod` 실행 시 `command not found` 오류
- 원인 가설: 권한 변경 명령 `chmod`의 철자를 잘못 입력함
- 확인: 오류 메시지를 확인하고 올바른 명령 `chmod 700 file.txt`를 실행한 뒤 `ls -l`로 권한 변경을 확인함
- 해결/대안: `chmod`로 다시 실행. 증거: [chmod.jpg](captures/chmod.jpg)

### 사례 2

- 문제: 따옴표만 입력해 Bash가 다음 입력을 기다리는 `>` 프롬프트로 바뀜
- 원인 가설: 닫는 따옴표가 없어 명령이 끝나지 않음
- 확인: `Ctrl+C`로 입력을 취소한 뒤 정상 명령을 실행함
- 해결/대안: `echo "practice_text" > note.txt`로 다시 입력하고 `cat note.txt`에서 내용을 확인함. 증거: [cp_rm_mv.jpg](captures/cp_rm_mv.jpg)

### 사례 3

- 문제: `docker logs e1-web`에 `"\x16\x03\x01..." 400` 같은 알아볼 수 없는 요청이 여러 줄 기록됨
- 원인 가설: `\x16\x03\x01`은 TLS(HTTPS) 핸드셰이크의 시작 바이트입니다. 브라우저가 `https://localhost:8080`으로 접속을 시도했는데, 컨테이너의 nginx는 80번 포트에서 HTTP만 받기 때문에 요청을 해석하지 못함
- 확인: 같은 시각(07:16:51) 이후 `GET / HTTP/1.1` 200 응답이 Chrome에서 정상 기록됨(07:17:08)
- 해결/대안: 주소를 `http://localhost:8080`으로 명시해 접속. 증거: [logs/docker_logs_e1-web.txt](logs/docker_logs_e1-web.txt)

## 핵심 개념

- **절대/상대 경로:** 절대 경로는 전체 위치(`C:\Users\ADMIN\E1-1\README.md`), 상대 경로는 현재 위치 기준(`.\README.md`)입니다.
- **권한:** Linux의 `r/w/x`는 읽기/쓰기/실행입니다. `755`는 소유자 `rwx`, 나머지 `r-x`; `644`는 소유자 `rw-`, 나머지 `r--`입니다. Windows ACL은 별도 모델입니다.
- **이미지/컨테이너:** 이미지는 실행에 필요한 파일과 설정의 묶음이고 컨테이너는 이미지에서 실행되는 격리 인스턴스입니다.
- **포트 매핑:** `-p 호스트포트:컨테이너포트`로 호스트 요청을 컨테이너 서비스로 전달합니다.
- **바인드 마운트/볼륨:** 바인드 마운트는 호스트 경로를 연결합니다. 볼륨은 Docker가 관리하며 컨테이너와 데이터의 생명주기를 분리합니다.
- **Git/GitHub:** Git은 로컬 변경 이력 관리 도구이고 GitHub는 원격 저장소 및 협업 플랫폼입니다.

## 제출 전 확인

- [ ] 실제 수행 명령과 출력으로 문서 갱신
- [x] Dockerfile 및 웹 소스 포함
- [ ] 포트 접속, 마운트 반영, 볼륨 영속성 증거 링크
- [x] 트러블슈팅 2건 이상
- [ ] 캡처에 명령과 출력 포함, 민감정보 제거/마스킹
- [ ] README에서 증거 파일 접근 가능
- [ ] GitHub 저장소 링크와 제출 권한 확인
