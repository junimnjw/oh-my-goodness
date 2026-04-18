# oh-my-godness

[@junimnjw](https://github.com/junimnjw)의 Claude Code 스킬 모음.

🇺🇸 [English README](README.md)

## 스킬

### [`pre-commit-review`](skills/pre-commit-review)

커밋 직전에 스테이징된 변경사항(`git diff --staged`)을 검토해 **버그와 로직 오류**를 찾습니다. Python과 TypeScript를 대상으로 하며, 스타일 지적보다 실제로 동작을 깨뜨릴 수 있는 문제에 집중합니다. Critical / Warning / Suggestion 심각도별로 정리된 리포트를 돌려줍니다.

## 요구 사항

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, 데스크톱 앱, 웹, 혹은 IDE 확장)

## 설치

Claude Code는 두 곳에서 스킬을 찾으며, 앞쪽이 우선순위가 높습니다:

- **개인 스킬** (`~/.claude/skills/`) — 모든 프로젝트에서 공통으로 사용
- **프로젝트 스킬** (`<프로젝트>/.claude/skills/`) — 해당 프로젝트에서만 사용, 같은 이름의 개인 스킬보다 우선

두 위치 모두 방식은 같습니다. `SKILL.md`가 들어있는 디렉토리를 놓아두면 Claude Code가 자동 인식합니다. 등록 단계는 없습니다.

### 개인 설치 (대부분의 경우 권장)

```bash
# 1. 디렉토리가 없으면 생성
mkdir -p ~/.claude/skills

# 2. 저장소를 임의의 위치에 clone (예: 임시 디렉토리)
git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness

# 3. 원하는 스킬만 복사
cp -r /tmp/oh-my-godness/skills/pre-commit-review ~/.claude/skills/
```

> **처음 설치할 때 주의:** Claude Code 세션이 시작될 때 `~/.claude/skills/`가 존재하지 않았다면, 디렉토리를 만들어도 세션이 그 위치를 감시하지 않습니다. 이 경우 **Claude Code를 한 번 재시작**해야 합니다. 이후에는 스킬 추가/수정/삭제가 현재 세션에 바로 반영됩니다.

### 프로젝트 설치 (특정 저장소 한정)

특정 프로젝트에서만 스킬을 쓰고 싶다면:

```bash
cd /path/to/your-project
mkdir -p .claude/skills

git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness
cp -r /tmp/oh-my-godness/skills/pre-commit-review .claude/skills/

# 팀원도 함께 쓰려면 커밋
git add .claude/skills/pre-commit-review
```

### 심볼릭 링크 옵션 (git pull로 자동 업데이트)

`git pull`로 업데이트를 받고 싶다면 복사 대신 심볼릭 링크를 거세요:

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/junimnjw/oh-my-godness.git ~/code/oh-my-godness

ln -s ~/code/oh-my-godness/skills/pre-commit-review ~/.claude/skills/pre-commit-review
```

### 설치 확인

```bash
ls ~/.claude/skills/pre-commit-review/SKILL.md
```

파일이 보이면 Claude Code에서 "커밋 전에 리뷰해줘" 같은 트리거 문구를 시도해보세요. 만약 Claude가 스킬을 인식하지 못하면 위 주의 사항대로 Claude Code를 한 번 재시작하세요.

## 사용법

변경사항을 스테이징한 뒤 Claude Code에게 리뷰를 요청하세요:

```bash
git add .
# 그런 다음 Claude Code에서:
# > 커밋 전에 리뷰해줘
# > diff 리뷰해줘
# > review my staged changes
```

Claude가 `git diff --staged`를 실행하고 필요한 파일을 읽은 뒤, **Critical / Warning / Suggestion** 심각도별로 findings을 정리하고 커밋해도 안전한지 최종 판단을 내려줍니다.

## 제거

```bash
rm -rf ~/.claude/skills/pre-commit-review
# 프로젝트 설치의 경우:
rm -rf /path/to/your-project/.claude/skills/pre-commit-review
```

## 기여

Issue와 PR 환영합니다. 새 스킬을 추가하려면 `skills/<스킬-이름>/` 아래에 `SKILL.md`를 루트로 둬주세요. 저장소 컨벤션은 [CLAUDE.md](CLAUDE.md) 참고.

## 라이선스

[MIT](LICENSE)
