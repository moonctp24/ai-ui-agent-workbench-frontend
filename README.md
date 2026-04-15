# 프로젝트 개요

GitHub 레포지토리 URL을 입력하면 백엔드가 소스 코드를 분석하고, **컴포넌트 계층·사용자 플로우·Mermaid 다이어그램·모바일 와이어프레임(PREVIEW)** 등을 한 화면에서 확인할 수 있는 Next.js 기반 워크벤치 UI입니다.

# 기술 스택

| 구분 | 기술 |
|------|------|
| 프레임워크 | Next.js 16 (App Router), React 19 |
| 언어 | TypeScript 5.7 |
| 스타일 | Tailwind CSS 4 |
| UI | Radix UI 기반(shadcn 패턴), lucide-react |
| HTTP | axios |
| 기타 | Mermaid(다이어그램), react-diff-viewer-continued(DIFF), react-markdown |

패키지 매니저: **yarn** (package.json의 `packageManager` 기준)

# 주요 기능 (UI 중심)

- Git URL 입력 후 **분석** → 진행 단계(SSE) 표시
- **PREVIEW**: LLM 생성 HTML을 iframe으로 표시(모바일 프레임), 트리 선택 시 하이라이트 연동
- **FLOW / DIAGRAM**: 사용자 플로우·Mermaid 다이어그램 표시
- **CODE / DIFF**: 영역 코드 보기·기획 수정 요청 후 diff 확인
- 좌측 패널: 컴포넌트/영역 트리, 분석 결과 텍스트, 체크박스·기획 수정
- 참고 라우트: `/design-system`, `/modal`, `/toast`, `/modify`, `/null` 등(데모·컴포넌트 목록)

# 폴더 구조

```
frontend/
├── app/                 # App Router 페이지
│   ├── page.tsx         # 메인 워크벤치
│   ├── layout.tsx
│   └── …                # design-system, modal, toast 등
├── components/          # 공용 컴포넌트·UI(shadcn)
├── lib/                 # 유틸(api 클라이언트 등)
├── hooks/
├── public/
├── package.json
└── README.md
```

# 실행 방법

## 설치

```bash
cd frontend
yarn install
```

## 개발 서버

```bash
yarn dev
```

기본적으로 Next.js는 `http://localhost:3000`에서 동작합니다. 백엔드 CORS 설정에 `localhost:3001`이 포함된 경우가 있으므로, 포트를 맞출 때는 백엔드 `main.py`의 `allow_origins`와 함께 조정하세요.

## 빌드·프로덕션

```bash
yarn build
yarn start
```

# 환경 변수

프로젝트 루트에 `.env` 또는 `.env.local`을 둘 수 있습니다. 예시:

```env
# 백엔드 API 베이스 URL (배포 시 설정 권장)
# NEXT_PUBLIC_API_URL=http://localhost:8001
```

현재 `lib/api.ts`는 개발 편의상 `http://localhost:8001`을 기본으로 두는 형태일 수 있습니다. 배포 시에는 `NEXT_PUBLIC_API_URL`을 실제 API 주소로 맞추고, `api.ts`에서 해당 변수를 읽도록 구성하는 것이 일반적입니다.

# API 연동 방식

- **클라이언트**: `axios` 인스턴스(`lib/api.ts`)로 `baseURL`에 백엔드 주소 설정
- **분석**: `POST /api/analyze-repo` — **Server-Sent Events(SSE)** 스트림으로 단계별 메시지·최종 JSON 수신
- **코드 수정**: `POST /api/modify-code` — JSON 요청/응답

CORS는 백엔드에서 허용 origin 목록으로 제어됩니다. 로컬에서 포트가 다르면 백엔드 CORS 설정에 프론트 origin을 추가해야 합니다.
