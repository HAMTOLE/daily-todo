# 오늘의 체크리스트 (daily-todo)

은비 님의 개인 할 일 체크리스트 — 휴대폰에서 여는 웹앱입니다.
노션 데이터베이스 `✅ 은비 오늘 할 일` 과 Cloudflare Worker(`family-notion`)를 거쳐 연결됩니다.

- 앱 주소: https://hamtole.github.io/daily-todo/
  (2026-11-25 가족 대시보드 저장소 안 `family-dashboard/daily-todo/` 에서 이 저장소로 옮겼습니다.)
- **첫 화면은 「전체 할 일」 보기입니다(v3, 2026-11-25).** 모든 미완료 할 일이 기한순(`지남` → `오늘` → `내일` → 이후 → `기한 없음`)으로 한 번에 뜹니다. 날짜 바에서 하루를 누르면 그날 기한만 걸러지고, 같은 날짜를 한 번 더 누르거나 `전체 보기` 를 누르면 전체로 돌아옵니다. 오늘 칸은 앰버 글씨·테두리로만 표시합니다.
- **비밀키는 `세줄일기`(`/sejul-diary/`)와 따로입니다.** (2026-11-25 분리)
  이 앱은 브라우저 `localStorage` 의 `dailyTodo.key` 에 자기 키를 두고, `/todos` 를 부를 때 `X-Todo-Key` 헤더로 보냅니다.
  같은 주소 계열(`hamtole.github.io`)의 세줄일기는 `sejul.diaryKey` + `X-Diary-Key` 를 쓰므로 두 앱이 키를 덮어쓰지 않습니다.
  기기에 키가 없으면 잠금 화면에서 한 번만 입력하면 됩니다.

## 이 저장소에 들어 있지 않은 것

공개 저장소이므로 다음은 코드에 넣지 않습니다.

- 노션 데이터베이스 ID, 통합 토큰
- Worker 비밀키 (`X-Todo-Key` 값)
- 실제 할 일 내용 (앱이 실행 중에 Worker에서 받아 옵니다)

앱은 Worker 주소(`https://family-notion.mynamebiya.workers.dev`)의 `/todos` 만 호출합니다.

## API (Worker `family-notion`)

| 요청 | 하는 일 |
| --- | --- |
| `GET /todos` | 미완료 전부 + 최근 7일 안에 완료한 항목 (기한 순) |
| `POST /todos` | 새 할 일 추가 `{title, category, due, source}` |
| `PATCH /todos/:id` | 완료 토글 `{done}` · 미루기 `{postpone:true}` · `{due}` · `{title}` · `{category}` |
| `DELETE /todos/:id` | 그 항목 보관(아카이브) |

모두 `X-Todo-Key` 헤더가 필요합니다. (세줄일기용 `X-Diary-Key` 로는 열리지 않습니다 — 서버가 `401 todo_auth_required` 로 막습니다.)

## 배포 방식 (중요)

- GitHub Pages 가 이 저장소의 **`gh-pages` 브랜치 루트**에서 서비스됩니다.
  (2026-11-25 기준. `main` 루트로 쓰고 싶으면 저장소 → Settings → Pages → Branch 를 `main` /(root) 로 바꾸면 됩니다.)
- 고친 내용은 `main` 에 커밋·푸시한 뒤, 라이브 사이트에도 반영하려면
  `git push -f origin main:gh-pages` 로 `gh-pages` 를 `main` 과 똑같이 맞춰 주세요.
  (`main` 만 푸시하면 사이트는 그대로입니다. 가족 대시보드와 같은 방식입니다.)

## 아이폰에 앱처럼 붙이기

Safari 로 주소를 연 뒤 **공유 → 홈 화면에 추가**. 아이콘과 전체 화면으로 열립니다.
