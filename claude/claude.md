# Claude Code 슬래시 명령어 완전 가이드

> Claude Code CLI에서 `/`로 시작하는 모든 슬래시 명령어 정리

---

## 빠른 참조 테이블

| 명령어 | 카테고리 | 한 줄 설명 |
|--------|----------|-----------|
| `/help` | 기본 | 도움말 및 사용 가능한 명령어 목록 표시 |
| `/exit` / `/quit` | 기본 | CLI 종료 |
| `/clear` / `/reset` | 세션 | 대화 기록 초기화 |
| `/resume` / `/continue` | 세션 | 이전 대화 이어서 계속하기 |
| `/branch` / `/fork` | 세션 | 현재 대화에서 브랜치 생성 |
| `/rename` | 세션 | 현재 세션 이름 변경 |
| `/memory` | 메모리 | CLAUDE.md 메모리 파일 편집 |
| `/context` | 메모리 | 현재 컨텍스트 사용량 시각화 |
| `/compact` | 메모리 | 대화 압축(컨텍스트 절약) |
| `/cost` | 통계 | 토큰 사용량 및 비용 정보 표시 |
| `/usage` | 통계 | 플랜 사용량 및 속도 제한 현황 |
| `/stats` | 통계 | 일일 사용량, 세션 히스토리, 모델 통계 |
| `/config` / `/settings` | 설정 | 설정 인터페이스 열기 |
| `/status` | 설정 | 버전, 모델, 계정, 연결 상태 표시 |
| `/model` | 설정 | AI 모델 선택/변경 |
| `/effort` | 설정 | 모델 노력 수준 설정 (low/medium/high/max/auto) |
| `/theme` | 설정 | 컬러 테마 변경 |
| `/color` | 설정 | 프롬프트 바 색상 설정 |
| `/keybindings` | 설정 | 키바인딩 설정 파일 열기 |
| `/terminal-setup` | 설정 | 터미널 키바인딩 설정 |
| `/statusline` | 설정 | 상태 줄 설정 |
| `/fast` | 설정 | 빠른 모드 토글 |
| `/mcp` | 도구 | MCP 서버 연결 관리 |
| `/chrome` | 도구 | Chrome 설정 관리 |
| `/plugin` | 도구 | Claude Code 플러그인 관리 |
| `/skills` | 도구 | 사용 가능한 스킬 목록 |
| `/ide` | 도구 | IDE 통합 관리 |
| `/hooks` | 도구 | 훅 설정 보기 |
| `/agents` | 에이전트 | 에이전트 설정 관리 |
| `/plan` | 에이전트 | 플랜 모드 진입 |
| `/add-dir` | 파일 | 작업 디렉토리 추가 |
| `/diff` | 파일 | 대화형 diff 뷰어 열기 |
| `/copy` | 파일 | 마지막 응답 클립보드에 복사 |
| `/export` | 파일 | 대화 내보내기 |
| `/security-review` | 코드 | 보안 취약점 분석 |
| `/install-github-app` | GitHub | GitHub Actions 앱 설치 |
| `/install-slack-app` | 통합 | Slack 앱 설치 |
| `/remote-control` / `/rc` | 원격 | 원격 제어 세션 활성화 |
| `/desktop` / `/app` | 앱 | 데스크톱 앱에서 계속하기 |
| `/mobile` / `/ios` / `/android` | 앱 | 모바일 앱 QR 코드 표시 |
| `/schedule` | 스케줄 | 예약 작업 생성/관리 |
| `/tasks` / `/bashes` | 스케줄 | 백그라운드 작업 목록 |
| `/login` | 계정 | Anthropic 계정 로그인 |
| `/logout` | 계정 | 계정 로그아웃 |
| `/voice` | 계정 | 음성 입력(PTT) 토글 |
| `/upgrade` | 계정 | 플랜 업그레이드 페이지 열기 |
| `/extra-usage` | 계정 | 추가 사용량 설정 |
| `/passes` | 계정 | 친구에게 무료 주간 공유 |
| `/privacy-settings` | 계정 | 개인정보 설정 |
| `/doctor` | 진단 | 설치 및 설정 진단 |
| `/init` | 진단 | CLAUDE.md 가이드로 프로젝트 초기화 |
| `/insights` | 진단 | 세션 분석 리포트 생성 |
| `/powerup` | 진단 | 기능 튜토리얼 |
| `/release-notes` | 진단 | 변경 로그 보기 |
| `/feedback` / `/bug` | 진단 | 피드백 제출 |
| `/rewind` / `/checkpoint` | 고급 | 이전 시점으로 되감기 |
| `/btw` | 고급 | 대화 기록에 남기지 않고 빠른 질문 |
| `/sandbox` | 고급 | 샌드박스 모드 토글 |
| `/setup-bedrock` | 고급 | Amazon Bedrock 설정 |
| `/ultraplan` | 고급 | 원격 실행을 위한 울트라 플랜 모드 |

---

## 카테고리별 상세 설명

### 기본 명령어

#### `/help`
Claude Code에서 사용 가능한 모든 명령어와 단축키를 표시한다.

#### `/exit` (별칭: `/quit`)
Claude Code CLI를 종료한다.

---

### 세션 관리

#### `/clear` (별칭: `/reset`, `/new`)
현재 대화 기록을 초기화하고 컨텍스트를 비운다. 긴 대화로 인해 컨텍스트가 꽉 찼을 때 유용하다.

#### `/resume [session]` (별칭: `/continue`)
이전에 저장된 대화를 이어서 계속한다. 인자 없이 사용하면 세션 선택기가 열린다.
```
/resume auth-refactor
/resume  # 세션 선택기 열기
```

#### `/branch [name]` (별칭: `/fork`)
현재 대화 시점을 기준으로 새로운 브랜치를 생성한다. 다른 방향으로 실험하고 싶을 때 유용하다.
```
/branch feature-experiment
```

#### `/rename [name]`
현재 세션의 이름을 변경한다. 이름을 생략하면 자동으로 생성된다.
```
/rename my-feature-work
```

---

### 컨텍스트 & 메모리

#### `/memory`
CLAUDE.md 메모리 파일을 편집하거나 자동 메모리를 활성화/비활성화한다. Claude가 다음 대화에서도 기억해야 할 내용을 관리할 수 있다.

#### `/context`
현재 컨텍스트 창의 사용량을 시각적으로 표시하고 최적화 제안을 제공한다.

#### `/compact [instructions]`
대화를 요약해서 압축하여 컨텍스트 공간을 확보한다. 포커스할 내용을 지정할 수 있다.
```
/compact
/compact focus on authentication logic
```

#### `/cost`
현재 세션에서 사용한 토큰 수와 예상 비용을 표시한다.

#### `/usage`
현재 플랜의 사용량 한도와 속도 제한 현황을 보여준다.

#### `/stats`
일일 사용량, 세션 히스토리, 연속 사용 현황(streaks), 선호 모델 등의 통계를 시각화한다.

---

### 설정

#### `/config` (별칭: `/settings`)
테마, 모델, 출력 스타일 등 다양한 설정을 조정할 수 있는 인터페이스를 연다.

#### `/status`
Claude Code의 버전, 현재 모델, 계정 정보, 연결 상태를 표시한다. Claude가 응답 중에도 사용 가능하다.

#### `/model [model]`
사용할 AI 모델을 선택하거나 변경한다. 인자 없이 사용하면 선택기가 열리고, 좌우 화살표로 노력 수준을 조절할 수 있다.
```
/model opus
/model sonnet
```

#### `/effort [low|medium|high|max|auto]`
모델의 노력 수준을 설정한다.
- `low`, `medium`, `high`: 설정이 유지됨
- `max`: 현재 세션에서만 적용
- `auto`: 기본값으로 초기화
```
/effort high
/effort max
```

#### `/theme`
UI 컬러 테마를 변경한다. 라이트, 다크, 색맹 접근성, ANSI 변형 등을 지원한다.

#### `/color [color|default]`
현재 세션의 프롬프트 바 색상을 설정한다.
사용 가능한 색상: `red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, `cyan`, `default`
```
/color red
/color default
```

#### `/keybindings`
키바인딩 설정 파일을 열거나 생성한다.

#### `/terminal-setup`
Shift+Enter 등의 터미널 키바인딩을 설정한다.

#### `/statusline`
Claude Code 상태 줄의 표시 방식을 설정한다.

#### `/fast [on|off]`
빠른 모드를 토글한다. 동일한 모델을 더 빠른 출력 속도로 사용한다.
```
/fast on
/fast off
```

---

### 도구 & 통합

#### `/mcp`
MCP(Model Context Protocol) 서버 연결을 관리하고 OAuth 인증을 처리한다.

#### `/chrome`
Chrome에서의 Claude 설정을 관리한다.

#### `/plugin`
Claude Code 플러그인을 설치, 관리한다.

#### `/skills`
현재 사용 가능한 스킬(커스텀 명령어) 목록을 표시한다.

#### `/ide`
IDE 통합 상태를 관리하고 표시한다.

#### `/hooks`
도구 이벤트에 응답하는 훅 설정을 확인한다.

---

### 에이전트

#### `/agents`
에이전트 및 서브에이전트 설정을 관리한다.

#### `/plan [description]`
플랜 모드로 직접 진입한다. 코드를 작성하기 전에 구현 계획을 세우고 검토할 수 있다.
```
/plan fix the auth bug
/plan implement user dashboard
```

---

### 파일 & 코드 작업

#### `/add-dir <path>`
현재 세션에서 접근 가능한 작업 디렉토리를 추가한다.
```
/add-dir ../apps
/add-dir /home/user/projects
```

#### `/diff`
커밋되지 않은 변경사항과 대화별 diff를 보여주는 대화형 diff 뷰어를 연다.

#### `/copy [N]`
마지막 어시스턴트 응답을 클립보드에 복사한다. 숫자를 지정하면 N번째 최근 응답을 복사한다. 선택기에서 `w`를 눌러 파일에 쓸 수도 있다.
```
/copy      # 마지막 응답 복사
/copy 2    # 두 번째 최근 응답 복사
```

#### `/export [filename]`
현재 대화를 평문 텍스트로 내보낸다.
```
/export conversation.txt
```

---

### 코드 리뷰 & 보안

#### `/security-review`
현재 브랜치의 보류 중인 변경사항에서 보안 취약점을 분석한다.

---

### GitHub & CI/CD

#### `/install-github-app`
Claude GitHub Actions 앱을 저장소에 설치한다.

---

### 통합 서비스

#### `/install-slack-app`
Claude Slack 앱을 설치한다. OAuth 흐름을 완료하기 위해 브라우저가 열린다.

#### `/remote-control` (별칭: `/rc`)
현재 세션을 claude.ai에서 원격으로 제어할 수 있도록 활성화한다.

#### `/remote-env`
`--remote` 옵션으로 시작한 웹 세션의 기본 원격 환경을 설정한다.

---

### 데스크톱 & 모바일

#### `/desktop` (별칭: `/app`)
현재 세션을 Claude Code 데스크톱 앱에서 계속한다.

#### `/mobile` (별칭: `/ios`, `/android`)
Claude 모바일 앱 다운로드용 QR 코드를 표시한다.

---

### 스케줄링 & 작업

#### `/schedule [description]`
클라우드 예약 작업을 생성, 업데이트, 목록 확인, 실행한다.
```
/schedule run tests daily at 9am
```

#### `/tasks` (별칭: `/bashes`)
백그라운드 작업 목록을 확인하고 관리한다.

---

### 계정 & 인증

#### `/login`
Anthropic 계정에 로그인한다.

#### `/logout`
현재 계정에서 로그아웃한다.

#### `/voice`
PTT(Push-to-Talk) 음성 입력을 토글한다.

#### `/upgrade`
더 높은 플랜으로 업그레이드할 수 있는 페이지를 연다.

#### `/extra-usage`
속도 제한에 걸렸을 때 계속 작업할 수 있도록 추가 사용량을 설정한다.

#### `/passes`
자격이 있는 경우 친구에게 Claude Code 무료 주간을 공유한다.

#### `/privacy-settings`
개인정보 설정을 확인하고 업데이트한다 (Pro/Max 플랜 전용).

---

### 진단 & 도움말

#### `/doctor`
Claude Code 설치 및 설정을 진단하고 검증한다.

#### `/init`
프로젝트에 CLAUDE.md 가이드 파일을 생성하여 초기화한다.

#### `/insights`
Claude Code 세션을 분석한 리포트를 생성한다.

#### `/powerup`
간단한 인터랙티브 레슨을 통해 Claude Code 기능을 발견한다.

#### `/release-notes`
인터랙티브 버전 선택기에서 변경 로그를 확인한다.

#### `/feedback [report]` (별칭: `/bug`)
Claude Code에 대한 피드백이나 버그 리포트를 제출한다.
```
/feedback bug in diff viewer
/bug       # 빠른 버그 리포트
```

#### `/reload-plugins`
활성화된 모든 플러그인을 재로드하여 변경 사항을 적용한다.

---

### 고급 명령어

#### `/rewind` (별칭: `/checkpoint`)
대화 및/또는 코드를 이전 시점으로 되감는다. 특정 메시지부터 요약하는 것도 가능하다.

#### `/btw <question>`
대화 기록에 남기지 않고 빠른 사이드 질문을 한다. 컨텍스트에 영향을 주지 않는다.
```
/btw what was that config file name?
```

#### `/sandbox`
샌드박스 모드를 토글한다 (지원 플랫폼에서만 사용 가능).

#### `/setup-bedrock`
Amazon Bedrock 인증, 리전, 모델 핀을 설정한다.

#### `/ultraplan <prompt>`
울트라플랜 세션에서 계획을 작성하고, 브라우저에서 검토한 후 원격 또는 로컬에서 실행한다.
```
/ultraplan implement user authentication
```

#### `/stickers`
Claude Code 스티커를 주문한다.

---

## 특수 문법

| 문법 | 설명 | 예시 |
|------|------|------|
| `/` 단독 입력 | 명령어/스킬 선택 메뉴 열기 | `/` 입력 후 문자로 필터링 |
| `!` 로 시작 | Bash 모드 - 직접 쉘 명령어 실행 | `! npm test` |
| `@` 입력 | 파일 경로 자동완성 트리거 | `@ src/components/` |

---

## 알아두면 좋은 점

1. **명령어 필터링**: `/` 입력 후 문자를 타이핑하면 해당 문자가 포함된 명령어만 필터링된다.
   - 예: `/mod` → `/model`, `/mobile` 표시

2. **별칭(Aliases)**: 많은 명령어에 별칭이 있다.
   - `/exit` = `/quit`
   - `/clear` = `/reset` = `/new`
   - `/resume` = `/continue`

3. **MCP 프롬프트**: MCP 서버가 추가 명령어를 제공할 경우 `/mcp__<서버>__<프롬프트>` 형태로 사용 가능하다.

4. **플랜별 제한**: 일부 명령어는 플랜에 따라 제한될 수 있다.
   - `/privacy-settings`: Pro/Max 플랜 전용

5. **커스텀 명령어(Skills)**: 스킬을 만들어 `/` 메뉴에 나만의 명령어를 추가할 수 있다.
