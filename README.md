# 유재진 통합 포트폴리오 (Portfolio Web App)

가족 5인의 주식·ETF 포트폴리오를 Google Sheets와 동기화하여 한 화면에서 관리하는 정적 웹앱입니다.
실시간 환율(USD/JPY), 계좌별 손익 분석, 수동 현재가 입력, 사진 OCR, 엑셀 가져오기 등을 지원합니다.

---

## 이번 수정에서 고친 문제 (Changelog)

1. **환율·가격 갱신 안정화** — 단일 CORS 프록시(corsproxy.io)에만 의존하던 구조를
   `allorigins → corsproxy → codetabs → thingproxy` **4단 자동 폴백 체인**으로 교체.
   프록시 하나가 죽어도 환율/현재가가 계속 갱신됩니다.
2. **동기화 오류 처리 강화** — `apiGet`에 **15초 타임아웃 + 1회 자동 재시도**,
   `apiPost`에 20초 타임아웃 추가. 멈춤·무한대기 방지 및 명확한 오류 메시지.
3. **동기화 아이콘 회전 버그** — 누락돼 있던 `@keyframes spin` 추가 (동기화 중 아이콘이 실제로 회전).
4. **수동 현재가 입력 로직** — GOOGLEFINANCE 연동이 실패한 종목(가격 0)도 직접 입력 가능하도록 개선.
5. **모바일 레이아웃 보강** — 헤더 버튼 축소, 대시보드 분석표 가로 스크롤, 작은 화면 깨짐 완화.
6. **정렬 NaN 안전성** — 값이 비어있는 열 정렬 시 순서가 깨지던 문제 수정.
7. **가족 비교 기준일 자동화** — 하드코딩(2026-05-28) 제거, 접속일 자동 표시.
8. **PWA 파일 추가** — 참조만 되고 없던 `manifest.json` + `icon.svg` 생성 (홈 화면 추가 가능).

---

## 파일 구성

| 파일 | 설명 |
|------|------|
| `index.html` | 앱 본체 (단일 파일) |
| `manifest.json` | PWA 매니페스트 |
| `icon.svg` | 앱 아이콘 |
| `README.md` | 본 문서 |

> 데이터 백엔드는 Google Apps Script Web App URL이며, 앱 우측 상단 **⚙️ → Web App URL**에서 설정합니다.

---

## GitHub Pages 배포 방법 (단계별)

> 아래 4개 파일을 저장소 **루트**에 올리면 됩니다: `index.html`, `manifest.json`, `icon.svg`, `README.md`

### 방법 A — 웹브라우저만으로 (가장 쉬움)

1. https://github.com 로그인 → 우측 상단 **+** → **New repository**
2. Repository name 입력 (예: `portfolio`) → **Public** 선택 → **Create repository**
3. 새로 만든 저장소 화면에서 **uploading an existing file** 클릭
4. 위 4개 파일을 **드래그&드롭** → 하단 **Commit changes**
5. 상단 **Settings → Pages** 이동
6. **Source**: `Deploy from a branch` → **Branch**: `main` / `/(root)` → **Save**
7. 1~2분 후 같은 화면 상단에 공개 주소가 표시됩니다:
   `https://<사용자명>.github.io/portfolio/`
8. 휴대폰 브라우저로 접속 → 메뉴 → **홈 화면에 추가** 하면 앱처럼 사용

### 방법 B — Git 명령어 (PC에 git 설치된 경우)

```bash
# 폴더 안에 index.html, manifest.json, icon.svg, README.md 가 있는 상태에서
git init
git add .
git commit -m "통합 포트폴리오 앱 (수정본)"
git branch -M main
git remote add origin https://github.com/<사용자명>/portfolio.git
git push -u origin main
```
이후 **Settings → Pages** 에서 위 6~7번과 동일하게 설정.

---

## 사용 메모

- 첫 실행 시 **⚙️ 설정**에서 Apps Script Web App URL을 넣어야 데이터가 뜹니다.
- `?owner=유재진` 처럼 URL 파라미터로 특정 사용자 화면을 고정 공유할 수 있습니다.
- 환율은 5분 캐시이며 ↺ 버튼/환율 카드 클릭 시 즉시 강제 갱신됩니다.
- 인터넷이 끊겨도 마지막 동기화 데이터(localStorage 캐시)로 조회됩니다.

> 무료 공개 CORS 프록시 특성상 환율/일부 해외 현재가는 일시적으로 지연될 수 있으나,
> 폴백 체인으로 가용성을 최대한 확보했습니다. 안정성이 더 필요하면 Apps Script 내에서
> 환율을 받아오도록 백엔드화하는 것을 권장합니다.
