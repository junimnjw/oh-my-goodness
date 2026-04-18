---
name: pre-commit-review
description: Review staged code changes for bugs and logic errors before committing. Use this skill whenever the user asks for a pre-commit review, wants to check their staged changes before `git commit`, says things like "review before I commit", "리뷰해줘 커밋 전에", "diff 리뷰", "커밋 전 체크", or references checking `git diff --staged` for Python or TypeScript code. Focuses on catching real bugs and logic errors rather than style nits.
---

# 커밋 전 리뷰 (Pre-Commit Review)

스테이징된 변경사항(`git diff --staged`)을 검토해 **버그와 로직 오류**를 찾는 스킬입니다. Python과 TypeScript 코드를 대상으로 하며, 목표는 스타일 지적이 아니라 실제로 동작을 망가뜨릴 수 있는 문제를 커밋 전에 잡아내는 것입니다.

## 언제 사용하나

사용자가 커밋 직전에 리뷰를 원할 때 발동합니다. 대표적인 요청 예시:

- "커밋 전에 리뷰해줘"
- "diff 리뷰해줘"
- "커밋하기 전에 체크해줘"
- "review my staged changes"
- "check what I'm about to commit"

스테이징된 변경사항이 없다면 그대로 사용자에게 알리고 중단하세요. 없는 문제를 지어내면 안 됩니다.

## 무엇을 찾는가

핵심은 **버그와 로직 오류**입니다. 런타임에 실제로 깨지거나 잘못된 결과를 만드는 문제를 우선 살피세요.

- **Off-by-one 오류** — 루프, 슬라이스, 범위 인덱싱에서 자주 발생
- **Null/undefined/None 역참조** — 값이 실제로 비어있을 수 있는 지점
- **잘못된 조건식** — 부울 로직 반전, 잘못된 연산자 (`==` vs `is`, `&&` vs `||`), 누락된 엣지 케이스
- **변형(mutation) 버그** — 반복 중 리스트/딕셔너리 수정, 호출자를 망가뜨리는 aliasing, 공유되는 mutable 기본값 (`def f(x=[])`)
- **타입 불일치** — 잘못된 형태의 값을 전달, Promise가 아닌 것을 await, 숫자 자리에 문자열
- **비동기/동시성 문제** — 누락된 `await`, 처리되지 않은 Promise rejection, race condition, Python `async` 함수를 `await` 없이 호출
- **리소스 누수** — 닫히지 않은 파일/연결, 누락된 `with`/`try-finally`
- **에러 처리 공백** — 삼켜지는 예외, 빈 `except:`, 진짜 에러를 숨기는 `catch`
- **잘못된 반환값** — 리스트가 와야 하는데 `None` 반환, cleanup을 건너뛰는 early return
- **정규식/파싱 버그** — 이스케이프 누락, greedy vs lazy, 인덱스 오프바이원
- **상태 머신 위반** — 초기화 전 객체 사용, 메서드 호출 순서 오류

언어별로 특히 주의할 부분:

**Python**
- Mutable 기본 인자 (`def f(x=[])`)
- 정수/실수 나눗셈 혼동 (`/` vs `//`)
- 싱글톤이 아닌 값에 `is` 사용 (`== vs is`)
- `dict.get(k)`가 반환한 `None`이 안전하지 않게 사용되는 경우
- 내장 이름 섀도잉 (`list`, `dict`, `id`, `type`)
- `for`/`else`, `while`/`else` 오용

**TypeScript**
- 실제 타입 불일치를 숨기는 `any`
- null일 수 있는 값에 non-null assertion (`!`) 적용
- `==` vs `===` (특히 `null`/`undefined`와 함께)
- async 함수에서 누락된 `await` — 값 대신 Promise를 반환하게 됨
- 변형 없는 배열 메서드(`map`, `filter`)를 변형하는 것처럼 사용
- 값이 반드시 있어야 하는 자리에서 optional chaining(`?.`)이 버그를 가리는 경우

## 무엇은 지적하지 않는가

잔소리만 많은 스킬은 외면받습니다. 실제로 버그를 일으키지 않는 다음 항목은 건너뛰세요:

- 순수 스타일 (공백, 따옴표, trailing comma)
- 네이밍 취향 (이름이 오해를 불러일으키는 수준이 아니라면)
- "더 idiomatic하게 쓸 수 있다"는 제안
- docstring/주석 누락
- 동작을 바꾸지 않는 미세 성능 최적화
- 테스트 커버리지 제안 (별개의 관심사)

diff가 깨끗하면 "깨끗하다"고 답하세요. 진중해 보이려고 가짜 문제를 만들면 안 됩니다.

## 리뷰 실행 방법

1. `git diff --staged`로 스테이징된 변경사항을 얻습니다. 비어있으면 `git diff`도 확인해 사용자가 스테이징을 잊은 건 아닌지 보세요 — 그런 경우 알려주고 물어보세요.
2. 필요하면 변경된 파일을 읽어 문맥을 확인하세요. diff만으로는 중요한 주변 코드(호출자, 타입 정의, 깨지는 invariant)를 놓칠 수 있습니다.
3. 각 발견 사항은 실제 버그인지 코드를 따라가며 검증하세요. 확신이 없다면 Critical이 아니라 Suggestion으로 표기하세요.
4. 아래 형식으로 리포트를 출력하세요.

## 출력 형식

다음 구조를 그대로 사용하세요:

```
# Pre-Commit Review

**Files reviewed:** <count> file(s), <+added>/<-removed> lines

## Critical
<확실하게 버그를 일으킬 이슈 — 커밋 전에 반드시 수정>

## Warning
<버그일 가능성이 높거나 위험한 패턴 — 수정 권장>

## Suggestion
<한번 더 살펴볼 만한 가능성 — 선택 사항>

## Summary
<한두 문장: 커밋해도 안전한가, 아닌가?>
```

각 발견 사항은 다음 형태를 따릅니다:

```
### <짧은 제목>
**File:** `path/to/file.py:42`
**Issue:** <무엇이 잘못되었는가>
**Why it's a bug:** <구체적인 실패 시나리오 — 무엇이, 언제 깨지는가>
**Suggested fix:** <문제를 해결하는 최소한의 변경>
```

특정 심각도에 해당하는 findings이 없다면 섹션을 누락하지 말고 `_None_`이라고 적으세요. 검토가 실제로 이루어졌음을 명확히 하기 위해서입니다.

## 예시

**예시 1 — 실제 버그 발견:**

입력 (Python diff):
```python
+def get_user_name(users, user_id):
+    for user in users:
+        if user.id == user_id:
+            return user.name
```

출력:
```
### 미발견 케이스에서 return 누락
**File:** `users.py:3`
**Issue:** `user_id`가 `users`에 없을 때, 함수가 끝까지 떨어지면서 암묵적으로 `None`을 반환합니다.
**Why it's a bug:** 결과를 문자열로 쓰는 호출자(예: `f"Hello {get_user_name(...)}"`)는 "Hello None" 같은 결과를 얻게 되어, not-found 케이스를 처리하지 못합니다.
**Suggested fix:** 명시적으로 `raise KeyError(user_id)`를 던지거나, 반환 타입을 `str | None`으로 표기하고 문서화하세요.
```

**예시 2 — 깨끗한 diff:**

실제 문제가 없는 diff라면 이렇게 출력하세요:
```
# Pre-Commit Review

**Files reviewed:** 1 file, +12/-3 lines

## Critical
_None_

## Warning
_None_

## Suggestion
_None_

## Summary
커밋해도 안전해 보입니다. 동작이 유지되는 단순한 리팩토링입니다.
```

가짜 findings으로 내용을 채우지 마세요. 깨끗한 리뷰는 유효한 결과입니다.

## 어조

단도직입적으로 쓰세요. 사용자는 문제를 찾아달라고 요청한 것이지, 격려를 바라는 게 아닙니다. 진짜 문제를 "고려해볼 수도 있다" 같은 완곡한 표현으로 누그러뜨리지 말고 "X일 때 실패합니다"라고 명확히 쓰세요. 반대로 심각도를 과장하지도 마세요. Suggestion은 Suggestion으로 남겨두세요.
