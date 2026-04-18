# oh-my-godness

[@junimnjw](https://github.com/junimnjw)의 Claude Code 스킬 모음.

🇺🇸 [English README](README.md)

## 스킬

### [`pre-commit-review`](skills/pre-commit-review)

커밋 직전에 스테이징된 변경사항(`git diff --staged`)을 검토해 **버그와 로직 오류**를 찾습니다. Python과 TypeScript를 대상으로 하며, 스타일 지적보다 실제로 동작을 깨뜨릴 수 있는 문제에 집중합니다. Critical / Warning / Suggestion 심각도별로 정리된 리포트를 돌려줍니다.

## 요구 사항

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, 데스크톱 앱, 웹, 혹은 IDE 확장)

## 설치

Claude Code는 `~/.claude/skills/` 디렉토리에서 스킬을 불러옵니다. 이 디렉토리 아래에 `SKILL.md` 파일이 있는 폴더가 있으면 그 이름으로 스킬이 등록됩니다.

### 방법 A: 스킬 하나만 설치 (권장)

원하는 스킬을 골라서 스킬 디렉토리에 복사:

```bash
# 스킬 디렉토리 생성
mkdir -p ~/.claude/skills

# 저장소를 임의의 위치에 clone (예: 임시 디렉토리)
git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness

# 원하는 스킬만 복사
cp -r /tmp/oh-my-godness/skills/pre-commit-review ~/.claude/skills/
```

### 방법 B: 심볼릭 링크 (git pull로 자동 업데이트)

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

파일이 보이면 Claude Code가 다음 세션부터 스킬을 인식합니다. Claude Code 세션을 새로 시작한 뒤 "커밋 전에 리뷰해줘" 같은 트리거 문구를 시도해보세요.

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
```

## 기여

Issue와 PR 환영합니다. 새 스킬을 추가하려면 `skills/<스킬-이름>/` 아래에 `SKILL.md`를 루트로 둬주세요.

## 라이선스

[MIT](LICENSE)
