# Claude Code Plugins - 기술 문서

## 기술 스택 사양

### 핵심 기술

| 카테고리 | 기술 | 버전 | 용도 |
|---------|------|------|------|
| 플랫폼 | Claude Code | >= 1.0.0 | 플러그인 런타임 |
| 구조 | Plugin Directory Pattern | - | 플러그인 조직 |
| 문서화 | SKILL.md | - | 스킬 정의 |
| 설정 | YAML | 1.2 | 설정 파일 |
| 데이터 | JSON | - | 매니페스트, 메타데이터 |

### 플러그인 시스템

| 구성요소 | 형식 | 설명 |
|---------|------|------|
| Manifest | JSON | `.claude-plugin/plugin.json` |
| Skills | Markdown | SKILL.md + 모듈 |
| Agents | Markdown | YAML 프론트매터 + 시스템 프롬프트 |
| Commands | Markdown | 슬래시 명령어 정의 |
| Hooks | JSON | 이벤트 트리거 정의 |

---

## 개발 환경

### 필수 도구

| 도구 | 용도 | 설치 |
|-----|------|------|
| Claude Code | 런타임 | `npm install -g @anthropic-ai/claude-code` |
| Git | 버전 관리 | 시스템 기본 |
| VS Code | 편집기 (권장) | 선택적 |

### 디렉토리 구조 설정

```bash
# 플러그인 생성
mkdir -p plugins/my-plugin/.claude-plugin
mkdir -p plugins/my-plugin/{agents,skills,commands,hooks}

# 매니페스트 생성
touch plugins/my-plugin/.claude-plugin/plugin.json
```

### 매니페스트 템플릿

```json
{
  "name": "my-plugin",
  "description": "플러그인 설명",
  "version": "1.0.0",
  "author": {
    "name": "개발자명"
  }
}
```

---

## 테스트 전략

### 테스트 계층

| 레벨 | 범위 | 방법 |
|-----|------|------|
| 스킬 검증 | 개별 스킬 | SKILL.md 형식 검증 |
| 에이전트 검증 | 에이전트 정의 | YAML 프론트매터 검증 |
| 통합 테스트 | 플러그인 전체 | Claude Code에서 실행 |
| E2E 테스트 | 사용자 시나리오 | 수동 테스트 |

### 검증 기준

**스킬 검증**:
- YAML 프론트매터 필수 (name, description)
- description 최대 1024자
- 3인칭 설명문

**에이전트 검증**:
- YAML 프론트매터 필수 (name, description, tools)
- 유효한 도구 목록

---

## 배포 환경

### 배포 프로세스

```
1. 개발 완료
    │
    ├── 스킬/에이전트 검증
    │
2. 버전 업데이트
    │
    ├── plugin.json 버전 수정
    ├── CHANGELOG.md 업데이트
    │
3. 마켓플레이스 등록
    │
    ├── marketplace.json 업데이트
    │
4. 테스트
    │
    ├── 스테이징 환경 테스트
    │
5. 배포
    │
    └── Git 태그 생성
```

### 버전 관리

- **시맨틱 버저닝**: MAJOR.MINOR.PATCH
- **변경 로그**: CHANGELOG.md 필수
- **Git 태그**: `v1.0.0` 형식

---

## 품질 및 보안

### TRUST 5 원칙 적용

| 원칙 | 적용 상태 | 설명 |
|-----|---------|------|
| Test First | 적용 | 스킬/에이전트 검증 우선 |
| Readable | 적용 | SKILL.md 표준 형식 |
| Unified | 적용 | 일관된 플러그인 구조 |
| Secured | 적용 | 도구 권한 명시 |
| Trackable | 적용 | 버전 및 변경 로그 |

### 보안 정책

| 영역 | 정책 |
|-----|------|
| 도구 접근 | 매니페스트에 명시된 도구만 허용 |
| 파일 접근 | 플러그인 디렉토리 내 제한 |
| 네트워크 | 선택적 허용 (MCP 서버 통해) |

---

## 운영 및 모니터링

### 로그 관리

| 유형 | 위치 | 보존 |
|-----|------|------|
| 실행 로그 | `.moai/logs/` | 30일 |
| 에러 로그 | `.moai/logs/errors/` | 90일 |
| 임시 파일 | `.moai/temp/` | 7일 |

### 모니터링 항목

- 플러그인 로딩 성공률
- 에이전트 응답 시간
- 스킬 토큰 사용량
- 에러 발생 빈도

---

## 기술 제약 및 고려사항

### 제약사항

| 제약 | 설명 | 대응 |
|-----|------|------|
| 토큰 제한 | 컨텍스트 윈도우 한계 | 점진적 로딩 |
| 동시성 | 에이전트 순차 실행 | 우선순위 관리 |
| 상태 관리 | 세션 간 상태 비지속 | 외부 파일 저장 |

### 모범 사례

1. **스킬 크기**: SKILL.md 최대 500줄 권장
2. **참조 깊이**: 최대 1단계 참조
3. **설명문**: 3인칭, 트리거 용어 포함
4. **테스트**: Haiku, Sonnet, Opus 모두 테스트

---

## 히스토리

| 날짜 | 변경 | 설명 |
|-----|------|------|
| 2026-01-07 | 초기 기술 문서 생성 | Claude Code 플러그인 기술 스택 정의 |
