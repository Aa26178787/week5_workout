# week5_workout · 캡스톤 소개 페이지 과제

**nginx + Flask를 Compose로 실행하고, 본인의 소개 페이지에서 다운로드와 조회수를 확인합니다.** 이 저장소는 미완성 틀입니다. compose.yaml의 TODO를 완성하기 전에는 실행되지 않습니다.

## 1. 프로젝트 준비

[교수자 저장소](https://github.com/Mok2Lee/week5_workout)를 Fork하고, 본인 저장소에서 Codespaces를 열거나 VM/Windows에 clone합니다. 저장소 이름은 `week5_workout`으로 사용합니다.

## 환경 선택

1. Windows에서는 NAS의 Docker Desktop 설치 파일로 설치하고 실행합니다. WSL 2 설치·업데이트나 재부팅을 요구하면 안내를 따릅니다.
2. `docker version`에서 Client와 Server, `docker compose version`에서 버전을 확인합니다. `docker run --rm hello-world`로 기본 실행을 확인합니다.
3. 교수자 안내에 따라 4장의 Ubuntu VM에서 Docker 설치·이미지 다운로드·빌드를 확인합니다. 막히면 Codespaces로 전환합니다. 인증서 검증을 끄지 않습니다.
4. Codespaces는 본인 저장소의 **Code → Codespaces → Create codespace on main**으로 엽니다. 제공된 devcontainer 설정이 Docker 환경을 준비합니다. 최초 준비에는 시간이 걸립니다.

NAS 설치 파일만으로 이미지와 Python 라이브러리까지 준비되지는 않습니다. Dockerfile의 패키지 설치는 이미지 빌드 중 한 번 실행하며, 학생이 VM의 Python 환경에 Flask를 별도 설치할 필요는 없습니다.

**실습 명령은 Ubuntu VM 또는 Codespaces의 Bash 터미널에서 실행합니다.** Windows에서는 Docker Desktop의 Linux 컨테이너 모드를 사용합니다.

Ubuntu VM 설치와 sudo 사용은 [설치 안내](docs/ubuntu-docker.md)를 확인합니다. 기본 Ubuntu 설치에서는 아래 docker 명령 앞에 sudo를 붙입니다.

## 브라우저 접속

- Windows Docker Desktop: `http://localhost:8080`
- Ubuntu VM: VirtualBox에서 기존 SSH 포트 전달은 유지하고 웹 규칙을 추가합니다. 호스트 IP `127.0.0.1`, 호스트 포트 `8080`, 게스트 포트 `8080`, TCP. Windows에서 `http://localhost:8080`으로 접속합니다.
- Codespaces: **Ports**의 `8080`에서 브라우저 열기를 선택합니다. 학생마다 주소가 다릅니다. 포트 공개 범위는 기본 Private로 유지합니다.

API 확인 주소는 위 웹 주소 뒤에 `/api/health`를 붙입니다. Codespaces 페이지 안의 API 요청도 같은 주소를 사용합니다.

## 2. 소개 페이지 제작

nginx/html/index.html에 프로젝트명·소개·주요 기능·사용 기술·팀 소개를 작성합니다. AI로 HTML·CSS를 제작해도 됩니다. 사진은 nginx/html 안에 넣고 상대 경로로 연결합니다.

API 연결을 위해 아래 id와 app.js 연결은 유지합니다.

| 화면 요소 | 유지할 id |
|---|---|
| 프로젝트명 | project-title |
| 소개글 | project-summary |
| 주요 기능 | project-features |
| 사용 기술 | project-technology |
| 다운로드 버튼 | download-button |
| 조회수 표시·다시 확인 버튼 | view-count, refresh-views |
| 결과 메시지 | message |

AI 요청 예시: “캡스톤 소개 정적 HTML을 작성해 줘. 제공한 id와 app.js 연결을 유지하고, 별도 외부 API나 프레임워크 없이 HTML·CSS로 구성해 줘. 프로젝트 내용은 다음과 같아: …”

## 3. Compose 완성

- api의 빌드 폴더: `./api`
- nginx 공개 포트: `8080:80`
- nginx 설정과 html 폴더 연결은 제공된 값을 확인합니다.
- api에 `DATA_DIR: /data` 환경변수를 지정합니다.
- api의 `/data`에 이름 있는 볼륨 `view-data`를 연결합니다.
- 최상위 `volumes`에서 `view-data`를 선언합니다.
- nginx는 `api:5000`으로 API 요청을 전달합니다. API 포트는 직접 공개하지 않습니다.

## 4. 실행과 기능 확인

프로젝트 최상위 폴더에서 실행합니다.

```bash
docker compose config
docker compose up -d --build
docker compose ps
```

1. 브라우저에서 nginx 주소의 `/api/health`를 열어 정상 응답을 확인합니다.
2. 메인페이지에 본인의 소개 내용이 표시되는지 확인합니다.
3. **소개 내용 다운로드**를 눌러 project-intro.md를 열고 화면 내용과 비교합니다.
4. 새로고침하면 조회수가 증가하는지 확인합니다.
5. **조회수 다시 확인**만 누르면 수치가 증가하지 않는지 확인합니다.
6. 아래 명령으로 컨테이너를 재생성한 뒤 `/api/views`를 직접 열어 이전 수치가 유지되는지 확인합니다. 메인페이지를 먼저 열면 조회수가 1 증가합니다.

```bash
docker compose down
docker compose up -d
```

`down -v`는 데이터 볼륨까지 삭제하므로 보존 확인 과정에서 사용하지 않습니다. 조회수는 방문자 수가 아닌 페이지 로드 횟수이며, 새로고침도 포함합니다.

## API 역할

| 요청 | 동작 |
|---|---|
| GET /api/health | 기본 연결 확인 |
| POST /api/download | 화면의 소개 내용으로 Markdown 파일 생성 |
| POST /api/views | 조회수 1 증가 후 현재 값 반환 |
| GET /api/views | 증가 없이 현재 조회수 반환 |

다운로드는 화면의 실제 텍스트를 전송합니다. 고정 파일 링크로 대체하지 않습니다. SQLite 저장·파일 응답·브라우저 API 호출 코드는 제공하며, 학생은 소개 내용과 서비스 설정을 완성합니다.

## 제출 확인

- 본인 GitHub 저장소에 완성한 HTML·CSS·Compose 파일 반영
- 소개 페이지와 다운로드한 파일
- 조회수 증가·읽기 및 컨테이너 재생성 후 유지 결과
- 두 서비스가 실행 중인 상태

기한·배점·최종 제출 형식은 LMS 공지를 따릅니다. 완료 후 컨테이너와 Codespaces를 정지합니다. API 수정 시에는 실습과 같이 재빌드 후 nginx를 재시작합니다.

