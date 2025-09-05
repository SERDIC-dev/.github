# SERDIC GITHUB Development Guide

최종 업데이트: 2025.08.25.

작성자: Chansoo Park

---

SERDIC Github 레포지토리 생성 규칙 및 공통 가이드 문서

각 팀별 개별 문서는 별도 확인 바랍니다.

1. [AI Team](./AI_Team_GUIDE.MD)
2. [WEB Team](./WEB_Team_GUIDE.MD)

## 📑 목차
1. [레포지토리 생성 규칙](#레포지토리-생성-규칙)
2. [브랜치 관리 전략](#브랜치-관리-전략)
3. [커밋 메시지 규칙](#커밋-메시지-규칙)
4. [Pull Request 가이드](#pull-request-가이드)
5. [코드 리뷰 가이드](#코드-리뷰-가이드)
6. [보안 관리](#보안-관리)
7. [릴리즈 관리](#릴리즈-관리)
8. [이슈 관리](#이슈-관리)

---

## 📁 레포지토리 생성 규칙

### 프로젝트

개별 솔루션이 아닌, 전체 규모의 프로젝트인 경우 아래와 같이 작성합니다.

[PROJECT]_[프로젝트명]

프로젝트명의 경우, 영어로 작성합니다.

프로젝트설명에는 실제 과제/사업명과 동일하게 작성합니다.

```
Repository Name: PROJECT_AR-Interior
Repository Description: ICT 기반 개방형 혁신제품서비스 개발지원사업(증강현실 인테리어) # 실제 사업명과 동일하게 작성
```

### 솔루션

AI Object Detection, WebAR, WorkOrder 등 개별 솔루션인 경우 아래와 같이 작성합니다.

[SOLUTION]_[솔루션명]

```
Repository Name: SOLUTION_AI-Object-Detection
Repository Description: AI 기반 Object Detection Module.
```

## 🌿 브랜치 관리 전략

### 메인 브랜치
- `main`: 프로덕션 배포 브랜치 (항상 안정)
- `develop`: 개발 통합 브랜치 (다음 릴리즈 준비)

### 보조 브랜치
- `feature/기능명`: 새 기능 개발
- `bugfix/이슈명`: 버그 수정
- `hotfix/이슈명`: 긴급 수정
- `release/버전`: 릴리즈 준비

### 브랜치 명명 예시
```
feature/user-authentication
feature/api-payment-integration
bugfix/login-validation-error
hotfix/security-vulnerability-fix
release/v1.2.0
```

## 📝 커밋 메시지 규칙

### 형식
```
type: subject

body (선택사항)
```

### Type 종류
- `feat`: 새로운 기능
- `fix`: 버그 수정
- `docs`: 문서 수정
- `style`: 코드 스타일 변경
- `refactor`: 코드 리팩토링
- `test`: 테스트 코드
- `chore`: 빌드, 설정 변경

### 예시
```
feat: 사용자 로그인 API 추가

JWT 토큰 기반 인증 시스템 구현
- 로그인/로그아웃 엔드포인트 추가
- 토큰 검증 미들웨어 구현
```

## 🔄 Pull Request 가이드

### PR 제목
- 명확하고 간결하게 작성
- 커밋 메시지 규칙 동일 적용

### PR 설명 필수 항목
```markdown
## 변경 내용
- 주요 변경사항 요약

## 테스트
- [ ] 단위 테스트 통과
- [ ] 통합 테스트 통과
- [ ] 수동 테스트 완료

## 체크리스트
- [ ] 코드 리뷰 완료
- [ ] 테스트 코드 작성
- [ ] 문서 업데이트
```

### 승인 규칙
- 최소 1명 이상 리뷰어 승인 필요
- CI/CD 파이프라인 통과 필수
- 컨플릭트 해결 후 머지

## 👀 코드 리뷰 가이드

### 리뷰 우선순위
1. **보안**: 보안 취약점, 민감정보 노출
2. **기능**: 요구사항 충족, 버그 가능성
3. **성능**: 성능 이슈, 메모리 누수
4. **유지보수성**: 코드 가독성, 구조

### 리뷰 댓글 예시
```
🔒 Security: API 키가 하드코딩되어 있습니다. 환경변수로 이동해주세요.
🐛 Bug: null 체크 필요합니다.
💡 Suggestion: 이 로직을 함수로 분리하면 재사용성이 높아집니다.
✅ LGTM: 깔끔하게 구현되었습니다.
```

## 🔒 보안 관리

### 금지 항목 (절대 커밋 금지)
```
# API 키, 토큰, 비밀번호
API_KEY=abc123
DB_PASSWORD=secret

# 개인정보
user_email=john@company.com
phone_number=010-1234-5678

# 내부 시스템 정보
internal_server=192.168.1.100
admin_endpoint=/admin/secret
```

### .gitignore 필수 항목
```
# 환경 변수
.env
.env.local
.env.production

# 로그 파일
*.log
logs/

# 설정 파일
config/production.json
secrets/

# 빌드 산출물
dist/
build/
```

## 🚀 릴리즈 관리

### 버전 관리 (Semantic Versioning)
- `MAJOR.MINOR.PATCH` (예: 1.2.3)
- MAJOR: 호환성 깨지는 변경
- MINOR: 하위 호환 기능 추가  
- PATCH: 하위 호환 버그 수정

### 릴리즈 프로세스
1. `release/v1.2.0` 브랜치 생성
2. 버전 정보 업데이트
3. QA 테스트 진행
4. `main` 브랜치 머지
5. Git 태그 생성
6. 배포

## 🎯 이슈 관리

### 라벨 분류
- `bug`: 버그 리포트
- `feature`: 새로운 기능 요청
- `enhancement`: 기존 기능 개선
- `documentation`: 문서 관련
- `urgent`: 긴급 처리 필요

### 이슈 제목 규칙
```
[Bug] 로그인 시 세션 만료 오류
[Feature] 사용자 프로필 이미지 업로드 기능
[Enhancement] API 응답 속도 개선
```
