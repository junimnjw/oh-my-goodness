# oh-my-goodness

[@junimnjw](https://github.com/junimnjw)의 Claude Code 스킬과 에이전트 모음.

🇺🇸 [English README](README.md)

## 목록

### 스킬 (Skills)

- [`pre-commit-review`](skills/pre-commit-review) — 커밋 전 스테이징된 변경사항(`git diff --staged`)을 검토해 버그와 로직 오류를 찾습니다. Python / TypeScript 대상이며 Critical / Warning / Suggestion 심각도별로 정리.

### 에이전트 (Agents)

- [`code-wiki-writer`](.claude/agents/code-wiki-writer.md) — 지정한 소스 폴더를 분석해 **이중 언어(한글 + 영어)** 온보딩 문서를 생성합니다. `docs/wiki/ko/`와 `docs/wiki/en/`에 섹션별 Markdown을 작성하며, 프로젝트 성격(CLI, 웹앱, 라이브러리 등)에 맞춰 섹션을 자동 선택합니다. 재실행 시 기존 위키와 현재 코드를 대조해 **잘못된 내용은 수정**하고 낡은 경로는 업데이트합니다.
- [`weekly-report-writer`](.claude/agents/weekly-report-writer.md) — 해외 연구소 보고, 메모, PR 제목+링크, 소스 폴더, KPI 테이블처럼 섞여 있는 근거를 읽어 관리층 공유용 주간 보고로 정리합니다. 특히 임원/중간관리자 보고처럼 지표, 협의 성과, 이슈, 근거 기반 문체가 중요한 상황에 맞습니다.

## 요구 사항

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, 데스크톱 앱, 웹, 혹은 IDE 확장)

## 빠른 설치

아래 블록 중 하나를 복사-붙여넣기 하세요. 각 블록은 단독으로 동작합니다(clone → 설치 → 정리). "프로젝트" 블록은 대상 프로젝트 루트에서 실행하세요.

```bash
# pre-commit-review — 개인 설치 (모든 프로젝트에서 사용 가능)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p ~/.claude/skills && \
  cp -r "$TMP/skills/pre-commit-review" ~/.claude/skills/ && \
  rm -rf "$TMP"
```

```bash
# pre-commit-review — 프로젝트 설치 (현재 프로젝트에서만; 프로젝트 루트에서 실행)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p .claude/skills && \
  cp -r "$TMP/skills/pre-commit-review" .claude/skills/ && \
  rm -rf "$TMP"
```

```bash
# code-wiki-writer — 프로젝트 설치 (프로젝트 루트에서 실행)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p .claude/agents && \
  cp "$TMP/.claude/agents/code-wiki-writer.md" .claude/agents/ && \
  rm -rf "$TMP"
```

```bash
# weekly-report-writer — 프로젝트 설치 (프로젝트 루트에서 실행)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p .claude/agents && \
  cp "$TMP/.claude/agents/weekly-report-writer.md" .claude/agents/ && \
  rm -rf "$TMP"
```

> `~/.claude/skills/` 혹은 `~/.claude/agents/` 디렉토리가 기존에 없었다면, 설치 후 **Claude Code를 한 번 재시작**해야 해당 디렉토리를 감시하기 시작합니다.

심볼릭 링크 방식(`git pull`로 자동 업데이트)이나 개인/프로젝트 우선순위 등 세부 내용은 아래 상세 섹션을 참고하세요.

## 설치 — 스킬

Claude Code는 두 곳에서 스킬을 찾으며, 앞쪽이 우선순위가 높습니다:

- **개인 스킬** (`~/.claude/skills/`) — 모든 프로젝트에서 공통으로 사용
- **프로젝트 스킬** (`<프로젝트>/.claude/skills/`) — 해당 프로젝트에서만 사용, 같은 이름의 개인 스킬보다 우선

두 위치 모두 방식은 같습니다. `SKILL.md`가 들어있는 디렉토리를 놓아두면 Claude Code가 자동 인식합니다. 등록 단계는 없습니다.

### 개인 설치 (대부분의 경우 권장)

```bash
# 1. 디렉토리가 없으면 생성
mkdir -p ~/.claude/skills

# 2. 저장소를 임의의 위치에 clone
git clone https://github.com/junimnjw/oh-my-goodness.git /tmp/oh-my-goodness

# 3. 원하는 스킬만 복사
cp -r /tmp/oh-my-goodness/skills/pre-commit-review ~/.claude/skills/
```

> **처음 설치할 때 주의:** Claude Code 세션이 시작될 때 `~/.claude/skills/`가 존재하지 않았다면, 디렉토리를 만들어도 세션이 그 위치를 감시하지 않습니다. 이 경우 **Claude Code를 한 번 재시작**해야 합니다. 이후에는 스킬 추가/수정/삭제가 현재 세션에 바로 반영됩니다.

### 프로젝트 설치 (특정 저장소 한정)

```bash
cd /path/to/your-project
mkdir -p .claude/skills

git clone https://github.com/junimnjw/oh-my-goodness.git /tmp/oh-my-goodness
cp -r /tmp/oh-my-goodness/skills/pre-commit-review .claude/skills/

# 팀원도 함께 쓰려면 커밋
git add .claude/skills/pre-commit-review
```

### 심볼릭 링크 옵션 (git pull로 자동 업데이트)

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/junimnjw/oh-my-goodness.git ~/code/oh-my-goodness

ln -s ~/code/oh-my-goodness/skills/pre-commit-review ~/.claude/skills/pre-commit-review
```

### 설치 확인

```bash
ls ~/.claude/skills/pre-commit-review/SKILL.md
```

파일이 보이면 Claude Code에서 "커밋 전에 리뷰해줘" 같은 트리거 문구를 시도해보세요. 만약 인식이 안 되면 위 주의 사항대로 Claude Code를 한 번 재시작하세요.

## 설치 — 에이전트

에이전트도 같은 두 위치 모델을 따르며, 이 저장소의 `code-wiki-writer`와 `weekly-report-writer`는 **프로젝트 레벨** 에이전트로 제공합니다:

```bash
cd /path/to/your-project
mkdir -p .claude/agents

git clone https://github.com/junimnjw/oh-my-goodness.git /tmp/oh-my-goodness
cp /tmp/oh-my-goodness/.claude/agents/code-wiki-writer.md .claude/agents/
cp /tmp/oh-my-goodness/.claude/agents/weekly-report-writer.md .claude/agents/

# 팀원과 공유하려면 커밋
git add .claude/agents/code-wiki-writer.md .claude/agents/weekly-report-writer.md
```

모든 프로젝트에서 쓰고 싶다면 `~/.claude/agents/`에 복사하세요 (첫 설치 시 재시작 주의 사항은 동일).

### 설치 확인

```bash
ls .claude/agents/code-wiki-writer.md
```

Claude Code에서:

```
> src/ 폴더 온보딩 문서 만들어줘
> write onboarding docs for ./packages/core
```

Claude가 에이전트에 위임해서 `docs/wiki/ko/`와 `docs/wiki/en/` 아래에 페이지를 작성합니다.

## 사용법

### `pre-commit-review`

변경사항을 스테이징한 뒤 리뷰 요청:

```bash
git add .
# Claude Code에서:
# > 커밋 전에 리뷰해줘
# > diff 리뷰해줘
# > review my staged changes
```

결과: **Critical / Warning / Suggestion** 심각도별 findings과 최종 판단.

### `code-wiki-writer`

소스 폴더 경로와 함께 호출:

```
# Claude Code에서:
> @code-wiki-writer for src/
> src/billing 폴더 온보딩 위키 만들어줘
> docs/wiki 내용이 오래됐어, 업데이트해줘
```

결과: `docs/wiki/ko/`와 `docs/wiki/en/` 아래에 섹션별 Markdown 파일. 기존 위키가 있으면 통째로 덮어쓰지 않고 **현재 코드와 대조해 잘못된 내용만 수정**합니다.

### `weekly-report-writer`

보고 기간과 가지고 있는 근거를 함께 주면 됩니다:

```
# Claude Code에서:
> @weekly-report-writer summarize this week's progress from this repo
> 이번 주 작업 내역 바탕으로 팀 공유용 주간 보고 써줘
> 지난주 커밋 + 메모 보고 매니저용 업데이트 만들어줘
> 해외 연구소 메일, PR 링크, 지표 테이블 기준으로 임원 보고용 주간 보고 정리해줘
```

결과: Confluence, 사내 Discussion, PR 설명란 등에 바로 붙여넣을 수 있는 보고 본문 초안. 지표는 각 소과제 섹션 맨 앞에 배치하고 정확한 값 그대로 유지하며, 각 항목 끝에 대표 PR 링크 1개를 붙일 수 있고, 과장 없는 실무형 문체로 정리합니다.

## 제거

```bash
# 스킬
rm -rf ~/.claude/skills/pre-commit-review
rm -rf /path/to/your-project/.claude/skills/pre-commit-review

# 에이전트
rm /path/to/your-project/.claude/agents/code-wiki-writer.md
rm /path/to/your-project/.claude/agents/weekly-report-writer.md
rm ~/.claude/agents/code-wiki-writer.md
rm ~/.claude/agents/weekly-report-writer.md
```

## 기여

Issue와 PR 환영합니다. 새 스킬은 `skills/<스킬-이름>/SKILL.md`에, 새 에이전트는 `.claude/agents/<에이전트-이름>.md`에 두세요. 저장소 컨벤션은 [CLAUDE.md](CLAUDE.md) 참고.

## 라이선스

[MIT](LICENSE)
