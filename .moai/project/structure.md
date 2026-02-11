# Claude Code Plugins - 구조 문서

## 아키텍처 개요

이 프로젝트는 **플러그인 디렉토리 패턴**을 따르며, Claude Code의 공식 플러그인 구조를 기반으로 합니다.

```
cc-plugins/
├── .claude/                    # Claude Code 설정
│   ├── skills/                 # 도메인별 전문 지식
│   ├── agents/                 # 특화된 에이전트
│   └── commands/               # 슬래시 명령어
├── .moai/                      # MoAI-ADK 설정
│   ├── config/                 # 설정 파일
│   │   ├── config.yaml         # 메인 설정
│   │   └── sections/           # 모듈식 설정 섹션
│   └── project/                # 프로젝트 문서
├── plugins/                    # 배포 가능한 플러그인 패키지
│   └── astory-blog-writers/    # aStory 플러그인
├── marketplace.json            # 마켓플레이스 메타데이터
├── CLAUDE.md                   # 프로젝트 컨텍스트
└── README.md                   # 프로젝트 소개
```

---

## 모듈 구조

### 핵심 디렉토리

**`.claude/`** - Claude Code 네이티브 구조
- `skills/`: 도메인 전문 지식 (SKILL.md 형식)
- `agents/`: 목적별 에이전트 정의
- `commands/`: 사용자 슬래시 명령어

**`plugins/`** - 배포 가능한 플러그인
- 각 플러그인은 독립적인 디렉토리
- `.claude-plugin/plugin.json` 매니페스트 포함
- 완전한 skill, agent, command, hook 세트

### 플러그인 내부 구조

```
plugins/astory-blog-writers/
├── .claude-plugin/
│   └── plugin.json             # 플러그인 매니페스트
├── agents/                     # 8개 저자 에이전트
│   ├── writer-mentor.md
│   ├── writer-architect.md
│   ├── writer-developer.md
│   └── ...
├── skills/                     # 전문 지식
│   ├── personas/               # 페르소나 정의
│   ├── traits/                 # 16개 특성 DNA
│   ├── writing-standards/      # 작성 표준
│   └── research/               # 리서치 프로토콜
├── commands/                   # 슬래시 명령어
│   └── post.md
├── hooks/                      # 이벤트 훅
│   └── hooks.json
├── README.md
├── CHANGELOG.md
└── LICENSE
```

---

## 모듈 관계

### 의존성 맵

```
marketplace.json
    │
    ├── plugins/astory-blog-writers/
    │       │
    │       ├── skills/ ◄─── agents/ (참조)
    │       │     │
    │       │     ├── personas/
    │       │     ├── traits/
    │       │     └── writing-standards/
    │       │
    │       └── commands/ ◄─── hooks/ (트리거)
    │
    └── .claude/ (프로젝트 레벨)
            ├── skills/ (MoAI-ADK 스킬)
            └── agents/
```

### 데이터 흐름

1. **명령어 실행**: 사용자가 `/post` 명령 실행
2. **훅 트리거**: `PreToolUse` 훅이 컨텍스트 준비
3. **스킬 로딩**: 필요한 스킬이 점진적으로 로드
4. **에이전트 위임**: 선택된 저자 에이전트가 작업 수행
5. **결과 반환**: 생성된 콘텐츠를 사용자에게 전달

---

## 외부 통합

### Claude Code 통합

| 통합 포인트 | 설명 |
|------------|------|
| Plugin System | `.claude-plugin/plugin.json` 매니페스트 |
| Skill Loading | SKILL.md 3단계 점진적 로딩 |
| Agent Delegation | `Task(subagent_type="...")` 패턴 |
| Slash Commands | 사용자 정의 명령어 |

### MoAI-ADK 통합

| 통합 포인트 | 설명 |
|------------|------|
| Config System | `.moai/config/` 모듈식 설정 |
| Documentation | `.moai/project/` 프로젝트 문서 |
| Workflows | Plan-Run-Sync 워크플로우 |

---

## 아키텍처 결정 배경

### 결정 1: 플러그인 디렉토리 분리

**결정**: 각 플러그인을 `plugins/` 하위의 독립 디렉토리로 관리

**근거**:
- 독립적인 버전 관리 가능
- 플러그인별 완전한 테스트 가능
- 배포 단위의 명확한 경계

### 결정 2: SKILL.md 기반 문서화

**결정**: 모든 스킬은 SKILL.md 형식을 따름

**근거**:
- Claude Code 공식 권장 형식
- 3단계 점진적 로딩으로 토큰 효율성
- 일관된 문서 구조

### 결정 3: 모듈식 설정 구조

**결정**: `.moai/config/sections/`로 설정 분리

**근거**:
- 토큰 효율적 로딩
- 관심사 분리
- 유지보수 용이성

---

## 비기능 요구사항

### 성능

| 요구사항 | 목표값 |
|---------|--------|
| 스킬 로딩 시간 | < 500ms |
| 에이전트 응답 시간 | < 2s (첫 응답) |
| 토큰 효율성 | 기본 로딩 < 100 토큰/스킬 |

### 확장성

- 플러그인 수: 무제한
- 에이전트 수: 플러그인당 무제한
- 스킬 깊이: 최대 2레벨 참조

### 보안

- 플러그인 격리: 각 플러그인은 독립 컨텍스트
- 권한 관리: 도구 접근 권한 명시
- 코드 검증: 설치 전 매니페스트 검증

---

## 히스토리

| 날짜 | 결정 | 근거 |
|-----|------|------|
| 2026-01-07 | 초기 구조 설계 | Claude Code 플러그인 공식 패턴 채택 |
