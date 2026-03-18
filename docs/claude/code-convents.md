# 코드 컨벤션 가이드

## 브랜치 명명 규칙

브랜치는 티켓번호를 기반으로 작성됩니다:
- 기능 개발: `feature/{티켓번호}` (예: `feature/GDEV-1`)
- 긴급 수정: `fix/{티켓번호}` (예: `fix/GDEV-1`)

**주의**: `git push --force`는 절대 사용하지 않습니다.

## 커밋 형식

```
<티켓번호> <commit-message-summary>
<commit-message-body>

Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

**예시:**
```
GPRD-123 몬스터 세일 전시 데이터 I/O개발
- 몬스터 세일용 Entity 생성
- 몬스터 세일 데이터 I/O를 위한 Repository 코드 작성

Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

## Pull Request 규칙

### PR 생성 절차
1. **브랜치 전체 변경사항 확인**: `git log devel..HEAD`와 `git diff devel...HEAD --stat`
2. `.github/pull_request_template.md` 템플릿 참고
3. 템플릿 구조에 따라 PR 본문 작성
4. `gh pr create` 명령어로 PR 생성

### 브랜치 타입별 기본 대상 브랜치
- `feature/*` → `devel` 브랜치
- `release/*` → `main` 브랜치
- `hotfix/*` → `main` 브랜치
- `bug/*` → `devel` 브랜치

## 클래스 명명 규칙

### Controller
- Web Controller: `{Feature}Controller`
- REST API Controller: `{Feature}ApiController`
- 예: `BrandShopController`, `NotificationApiController`

### Service
- 기본: `{Feature}Service`
- 특화된 기능: `{Feature}{Action}Service`
- 예: `BrandShopService`, `KeyTranslationService`

### Repository
- 기본: `{Feature}Repository`
- 예: `BrandRepository`, `CategoryRepository`

### DTO (Data Transfer Object)
- 명명 규칙: `{Feature}{Action}{Request|Response}DTO{Version}`
- 버전 관리 필수 (V1, V2...)
- 예: `BrandShopContentResponseDTOV1`

### VO (Value Object)
- 명명 규칙: `{Feature}{Description}VO`
- **신규 코드는 반드시 Record class로 구현** (Java 21)
- 예: `BrandShopTopVO`, `WebMetaVO`

### Enum/Constant
- Enum: `{Feature}{Type}Enum` (예: `ErrorCodeEnum`)
- Constant: `{Feature}Constant` (예: `GlobalFrontPathConstant`)

### 기타
- Client: `{Feature}Client`
- Mapper: `{Feature}{Type}Mapper`
- Validator: `Valid{Feature}Validator`
- Exception: `{Type}Exception`

## 코드 아키텍처

**Controller → (Application) → Service → Repository** 레이어드 아키텍처를 따릅니다.

### 레이어별 책임

| 레이어 | 책임 | 어노테이션 |
|--------|------|-----------|
| Controller | HTTP 요청 처리, DTO 변환 | `@Controller`, `@RestController` |
| Application | 복잡한 유스케이스 조율 (선택적) | `@Service` |
| Service | 핵심 비즈니스 로직 | `@Service`, `@Transactional` |
| Repository | 데이터 접근, 외부 API 호출 | JPA Repository, QueryDSL |

### 패키지 구조

```
com.musinsa.global.{module}/
├── presentation/
│   ├── web/          # @Controller
│   └── api/          # @RestController
├── application/      # Application Service (선택적)
├── domain/
│   ├── constant/     # 상수, Enum
│   ├── service/      # Domain Service
│   ├── repository/   # Repository Interface
│   └── vo/           # Value Object
│       ├── in/       # 입력용 VO
│       └── out/      # 출력용 VO
├── infrastructure/
│   └── repository/   # Repository 구현체
└── dto/              # Data Transfer Object
```

## 환경 프로파일

- **local** - 로컬 개발 (테스트 기본값)
- **devel** - 개발 환경
- **alpha** - 스테이징 환경
- **prod** - 운영 환경

각 모듈은 환경별 YAML 파일 (`application-{profile}.yml`)을 가집니다.

## 다국어 지원

지원 언어: 한국어(KO), 영어(EN), 일본어(JA), 베트남어(VI)

메시지 파일 위치:
- `app-front/src/main/resources/messages/`
- `app-api/src/main/resources/messages/`
