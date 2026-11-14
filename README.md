# ai-dlc-orchestrator

**AI-DLC (AI-Driven Development Life Cycle)** 기반의 재사용 가능한 프로젝트 통합 조율 환경이다.

독립적으로 운영되는 Backend·Frontend AI-DLC의 공통 백로그, 기능 요구사항, 계약 변경 영향 및 통합 완료를 관리한다.

프로젝트별 연결 정보는 `ai-dlc.yaml`로 분리하며, 제품 구현과 테스트는 각 개발 저장소의 AI-DLC가 담당한다.

## 주요 기능

- **기능 아이디어 발굴:** 프로젝트 전체 관점에서 신규 기능을 검토하고 GitHub Projects에 최대 3개의 Pending Draft 등록
- **통합 백로그 정제:** 승인된 후보를 최대 2개 선정하고 Parent/Sub-issue 생성 및 공식 관계 연결
- **Issue 상세화:** 정식 Issue의 미결 사항을 사용자와 협의하고 개발 착수 승인과 최신 계약 기준 정리
- **공통 계약 관리:** 사용자 요청으로 계약 변경을 협의하고 영향 분석과 승인된 명세 PR 진행
- **변경 영향 분석:** Backend·Frontend·Specs PR과 공식 계약 변경의 프로젝트 간 영향 검토
- **GitHub Projects 연동:** 기능 후보, 승인 상태, 우선순위 및 통합 진행 상태 관리
- **공식 명세 참조:** 요구사항·API·인증·ERD·UI 계약의 공식 원본 조회
- **통합 완료 판단:** 관련 구현·계약·검증 결과를 종합해 완료 조건 확인
- **사용자 승인:** 중요한 요구사항·계약 변경 결정과 PR Merge는 사용자가 직접 수행

## 디렉터리 구조

```text
ai-dlc-orchestrator/
├── .agents/
│   └── skills/
│       ├── feature-discovery/
│       │   └── SKILL.md
│       ├── cross-project-backlog-refinement/
│       │   └── SKILL.md
│       ├── issue-refinement/
│       │   └── SKILL.md
│       ├── contract-management/
│       │   └── SKILL.md
│       └── integration-review/
│           └── SKILL.md
├── docs/
│   ├── workflow.md
│   └── integration-checklist.md
├── templates/
│   ├── .github/
│   │   ├── ISSUE_TEMPLATE/
│   │   │   └── feature_request.yml
│   │   └── pull_request_template.md
│   └── docs/
│       ├── requirements.md
│       ├── api-spec.md
│       ├── erd.md
│       └── ui-spec.md
├── AGENTS.md
├── ai-dlc.example.yaml
├── projects.example.yaml
├── projects.local.yaml  # 로컬 전용, Git 제외
├── integration-review-state.json
└── README.md
```

## 새 프로젝트에 적용하기

### 1. 프로젝트 등록

이 저장소를 공통 운영 루트로 열고 `projects.example.yaml`을 참고해 Git에서 제외되는 `projects.local.yaml`에 각 프로젝트의 이름, 별칭 목록과 절대 경로를 등록한다. 실제 로컬 경로와 비밀값은 공유 예시에 넣지 않는다. 이름과 별칭은 앞뒤 공백과 대소문자를 제외하고 프로젝트 간 중복될 수 없다. 경로는 대상 Git 작업 트리의 루트여야 한다.

공통 Skill·운영 문서·리뷰 기준·템플릿은 이 저장소에서 읽는다. 대상 저장소의 기존 운영 파일, 설정, 명세와 기록은 자동으로 복사·삭제하거나 덮어쓰지 않는다. Issue·PR 템플릿은 대상 저장소를 우선하고 없으면 공통 `templates/.github/`를 참고한다.

### 2. 공식 명세 준비

`templates/docs/`에는 공식 명세의 초기 작성 양식이 제공된다.

새 프로젝트에 명세 문서가 없다면 필요한 템플릿을 대상 저장소의 `docs/`에 복사하고 실제 내용을 작성한다.

이미 공식 명세가 존재한다면 기존 문서를 유지하고 템플릿으로 덮어쓰지 않는다.

- `requirements.md`: 기능·비기능 요구사항
- `api-spec.md`: API·인증·오류 처리 계약
- `erd.md`: 데이터 모델과 관계
- `ui-spec.md`: 화면·라우팅·사용자 동작

초기 템플릿은 작성·검토·승인 전까지 확정된 공식 명세로 간주하지 않는다.

### 3. 프로젝트 설정

`ai-dlc.example.yaml`을 대상 저장소의 루트에 `ai-dlc.yaml`이라는 이름으로 복사한다.

파일 안의 주석을 참고해 다음 정보를 설정한다.

- 서비스 식별자와 Orchestrator 운영 저장소
- Specs·Backend·Frontend 저장소 및 기준 브랜치
- GitHub Projects의 ID·필드·선택지
- Issue 라벨과 Parent/Sub-issue 관리 역할
- 공식 요구사항·API·인증·ERD·UI 명세 경로
- PR 변경 영향 분석 상태 파일

`[변경 필요]` 항목은 실제 값으로 교체하고, `[확인 필요]` 항목은 실제 프로젝트 환경과 대조한다.

Orchestrator를 Specs 저장소에 설치한다면 `orchestrator.repository`와 `repositories.common.repository`에 같은 저장소를 지정할 수 있다.

### 4. GitHub 환경 확인

- Orchestrator와 연결 저장소의 접근 권한을 확인한다.
- GitHub Project의 필드·선택지와 Issue 라벨이 실제로 존재하는지 확인한다.
- Parent/Sub-issue 공식 관계 생성·조회 기능과 권한을 확인한다.
- 공식 명세의 저장소·브랜치·경로 및 접근 가능 여부를 확인한다.
- `connection_status`와 `reference_commit`은 실제 확인된 값만 기록한다.
- `integration-review-state.json`은 새 프로젝트의 빈 초기 상태에서 시작한다.

### 5. AI-DLC 실행

Codex에서 공통 운영 루트를 열고 등록한 프로젝트 이름과 원하는 작업을 요청한다.

```text
Ask-Teacher 프로젝트 아이디어 제안해.
Ask-Teacher 프로젝트 백로그 정제해.
Ask-Teacher 프로젝트의 Parent Issue #123을 상세화해.
Ask-Teacher 프로젝트의 인증 계약 변경을 관리해.
Ask-Teacher 프로젝트 변경 영향 검토해.
```

각 Skill은 독립적으로 실행하며, 한 작업이 끝났다고 다른 Skill이나 Backend·Frontend 개발 사이클을 자동 시작하지 않는다.

Main은 등록에서 대상을 유일하게 식별하고 대상 설정·Git 루트·실제 원격과 적용되는 로컬 지시를 확인한다. 이름이 없거나 중복되거나 경로·설정·원격이 불명확하면 사용자에게 확인한다. 대상 설정·명세·상태 파일·작업 기록·Git 작업은 대상 루트 기준이다. 제품 구현·테스트는 각 구현 저장소의 AI-DLC가 담당한다. 필요한 작업의 테스트 명령이 설정되지 않았으면 추측하지 않는다.

`Ask-Teacher 프로젝트 작업 시작하자`처럼 작업 종류가 불명확한 요청에서는 대상을 확인한 뒤 원하는 작업을 묻는다. 프로젝트 이름만으로 특정 Skill을 자동 선택하지 않는다.

이는 문서 기반 절차다. 새 Codex 세션에서 공통 Skill이 실제 발견되는지와 대상 선택이 올바르게 작동하는지는 전환 시 별도로 확인해야 한다. 전환 검증 전 기존 프로젝트 운영 파일은 보존한다.

## 기본 운영 흐름

1. **기능 발굴:** `feature-discovery`가 프로젝트 전체 요구사항·기존 기능·후보를 검토하고 Pending Draft 등록
2. **사용자 승인:** 사용자가 GitHub Project에서 기능 후보의 승인 여부 결정
3. **백로그 정제:** `cross-project-backlog-refinement`가 Approved Draft를 정식 Issue로 전환하고 필요한 구현 Sub-issue 연결
4. **Issue 상세화:** `issue-refinement`가 사용자와 결정 사항을 협의하고 착수 승인을 확인
5. **계약 변경:** 필요 시 사용자가 `contract-management`를 직접 요청해 변경 협의·명세 PR을 진행하고 직접 Merge
6. **개별 개발:** Backend·Frontend AI-DLC가 승인된 Issue 기반 개발 절차 수행
7. **변경 영향 검토:** `integration-review`가 요청 시 PR·계약 변경과 소비자 영향을 분석하고 후속 작업 제안
8. **통합 완료 확인:** 관련 구현·계약·검증 증거와 Parent 완료 조건 확인

각 단계는 사용자 요청에 따라 독립적으로 수행된다. 자세한 절차는 `docs/workflow.md`, 통합 완료 기준은 `docs/integration-checklist.md`를 따른다.

## 운영 원칙

- 사용자는 PM이자 최종 승인자다.
- Orchestrator는 공통 조정만 담당하며 개별 Backend·Frontend AI-DLC의 개발 절차를 변경하지 않는다.
- 승인되지 않은 기능 후보를 정식 Issue로 전환하거나 개발을 시작하지 않는다.
- 중요한 요구사항·계약·인증·DB 변경은 사용자 결정에 따른다.
- 공식 명세는 설정된 원본을 기준으로 확인하고 구현 저장소의 사본으로 대체하지 않는다.
- PR 영향 분석은 실제 근거를 바탕으로 수행하며 검증되지 않은 결과를 성공으로 보고하지 않는다.
- 단순 Issue 종료나 Sub-issue 완료율만으로 통합 완료를 판단하지 않는다.
- PR Merge는 사용자가 직접 수행한다.
- 비밀값·토큰·인증 정보는 설정과 작업 기록에 포함하지 않는다.

## 변경 영향 분석 상태

`integration-review-state.json`은 PR 변경 영향 분석의 중복 작업을 줄이기 위한 상태 파일이다.

서비스별 저장소 조회 완료 시점과 PR별 최신 분석 상태를 관리하며, 해당 파일이 없거나 분석 상태를 확인할 수 없다면 이전 검토가 완료된 것으로 간주하지 않는다.

새 프로젝트에 적용할 때는 다음 초기 상태를 사용한다.

```json
{
  "schema_version": 1,
  "services": {}
}
```

상태 파일은 별도의 공식 명세나 개발 기록을 대체하지 않는다.

## 공통 AI-DLC 업데이트

공통 운영 루트를 사용하는 세션은 이 저장소의 Skill과 운영 문서를 참조한다. 기존 프로젝트의 로컬 지시도 적용 여부와 충돌을 확인하며, 런타임의 자동 발견이나 기존 세션의 갱신은 보장하지 않는다.

프로젝트별 설정인 `ai-dlc.yaml`, 공식 명세 문서 및 실제 분석 상태가 저장된 `integration-review-state.json`은 임의로 덮어쓰지 않는다.
