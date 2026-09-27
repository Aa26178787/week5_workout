# week5_workout · 캡스톤 소개 페이지 과제

**Windows Docker Desktop과 PowerShell에서 nginx·Flask를 Compose로 실행합니다.** 본인의 소개 페이지와 Compose 설정을 완성하고, health API에 본인 학번과 이름을 넣습니다.

|구분|이름·주소|
|---|---|
|과제 폴더|`C:\lab\week5hw`|
|Flask 이미지 / 컨테이너|`week5_03` / `week5_api02`|
|nginx 이미지 / 컨테이너|`week5_04` / `week5_web02`|
|네트워크 / 데이터 볼륨|`week5_net02` / `week5_data02`|
|웹 페이지|`http://localhost:8080`|

## 1. 프로젝트 준비

실습과 같은 Windows 실행 환경을 사용합니다. Docker Desktop을 실행하고 [교수자 저장소](https://github.com/Mok2Lee/week5_workout)를 Fork합니다. `내계정`을 바꿔 PowerShell에서 실행합니다.

```powershell
New-Item -ItemType Directory -Force C:\lab
Set-Location C:\lab
git clone https://github.com/내계정/week5_workout.git week5hw
Set-Location .\week5hw
code .
```

저장소 이름은 week5_workout, 로컬 폴더 이름은 week5hw입니다. 이후 명령은 `C:\lab\week5hw`에서 실행합니다. 실습 페이지가 실행 중이면 **`C:\lab\week5`에서 `docker compose down`** 후 과제를 실행합니다. 두 프로젝트가 같은 8080번 포트를 사용합니다.

## 2. 소개 페이지 제작

`nginx/html/index.html`에 프로젝트명·소개·주요 기능·사용 기술·팀 소개를 작성합니다. AI로 HTML·CSS를 제작해도 됩니다. 사진은 `nginx/html` 안에 넣고 상대 경로로 연결합니다.

|화면 요소|유지할 id|
|---|---|
|프로젝트명|project-title|
|소개글|project-summary|
|주요 기능|project-features|
|사용 기술|project-technology|
|다운로드 버튼|download-button|
|조회수 표시 / 다시 확인 버튼|view-count / refresh-views|
|결과 메시지|message|

**id와 app.js 연결을 유지**해야 제공된 API를 사용할 수 있습니다. AI 요청 예시: “캡스톤 소개 정적 HTML을 만들어 줘. 제공한 id와 app.js 연결을 유지하고 HTML·CSS로 구성해 줘. 프로젝트 내용은 다음과 같아: …”

## 3. Compose 완성

compose.yaml의 TODO를 완성합니다. Flask·nginx Dockerfile과 API 코드는 제공됩니다.

|작성 위치|완성할 값|
|---|---|
|api의 build|`./api`|
|nginx의 build|`./nginx`|
|nginx의 ports|`8080:80`|
|api의 DATA_DIR|`/data`|
|api의 volumes|`data:/data`|

두 이미지 이름은 `week5_03`과 `week5_04`, 컨테이너 이름은 `week5_api02`와 `week5_web02`입니다. 최상위 data 볼륨은 실제 이름 `week5_data02`로 제공됩니다. nginx는 `week5_api02:5000`으로 API 요청을 전달합니다.

### 학번·이름 등록

`api/app.py`의 `health()`에서 **`student_id`는 본인 학번, `name`은 본인 이름**으로 변경합니다. 학번은 문자열로 작성하고, `status: ok`는 유지합니다.

```json
{
  "status": "ok",
  "student_id": "본인 학번",
  "name": "본인 이름"
}
```

실행 후 `http://localhost:8080/api/health`에서 본인 정보가 나오는지 확인합니다. 이미 실행 중이었다면 `docker compose up -d --build api`와 `docker compose restart nginx`으로 수정 내용을 반영합니다.

## 4. 실행과 확인

```powershell
docker compose config
docker compose up -d --build
docker compose ps
curl.exe -i http://localhost:8080/api/health
```

HTTP 200, `status: ok`, **본인 학번(`student_id`)과 이름(`name`)**을 확인한 뒤 `http://localhost:8080`을 엽니다.

1. 본인의 소개 내용이 표시되는지 확인합니다.
2. **소개 내용 다운로드**로 받은 project-intro.md를 열고 화면의 소개·기능·기술 내용과 비교합니다.
3. 페이지를 새로고침하면 조회수가 1 증가하는지 확인합니다.
4. **조회수 다시 확인** 버튼만 누르면 값이 그대로인지 확인합니다.

이전 실습 화면의 JavaScript가 브라우저에 남아 버튼이 반응하지 않으면 **Ctrl+Shift+R**로 강력 새로고침합니다.

## 5. 데이터 보존 확인

```powershell
curl.exe http://localhost:8080/api/views
docker compose down
docker compose up -d
curl.exe http://localhost:8080/api/views
```

Flask가 시작한 뒤 마지막 명령을 실행하고 재생성 전후 수치가 같은지 확인합니다. 메인페이지를 먼저 열면 조회수가 증가하므로 비교할 때는 `/api/views`만 확인합니다. **`down -v`는 저장 볼륨까지 삭제**하므로 보존 확인에 사용하지 않습니다.

## 6. 수정과 제출

HTML·CSS 수정 후 `docker compose up -d --build nginx`으로 웹 이미지를 다시 빌드합니다. API 수정 후에는 `docker compose up -d --build api`, `docker compose restart nginx`을 실행합니다.

- 본인 GitHub 저장소에 HTML·CSS·Compose 파일과 수정한 `api/app.py` 반영
- `/api/health`에서 본인 학번·이름이 표시되는 응답 화면
- 소개 페이지와 실제 다운로드 파일
- 조회수 증가·다시 확인·컨테이너 재생성 후 유지 결과
- 두 서비스가 실행 중인 상태

기한·배점·최종 제출 형식은 LMS 공지를 따릅니다. 완료 후 `docker compose down`으로 종료합니다.

## 제공 API

|요청|역할|
|---|---|
|GET /api/health|기본 연결 확인 및 본인 학번·이름 반환|
|POST /api/download|현재 화면 내용으로 Markdown 파일 생성|
|POST /api/views|조회수 1 증가|
|GET /api/views|증가 없이 현재 값 확인|

조회수는 페이지 로드 횟수이며 새로고침도 포함합니다. 다운로드는 고정 파일 링크가 아니라 현재 화면의 소개 내용을 API에 보내 생성합니다.

Ubuntu VM 설치·연결 확인이 필요한 경우에만 [Ubuntu 참고 안내](docs/ubuntu-docker.md)를 확인합니다. 설치나 다운로드가 실패하면 Windows PowerShell 실습으로 진행합니다.
