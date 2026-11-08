# 블록 ↔ 텍스트 UI/UX 패턴 분석

> 조사 기준일: 2026-11-08

기술적으로 변환이 가능해도 UI가 나쁘면 학생은 표현 사이의 관계를 배우지 못합니다.

이 문서는 서비스별 "어떻게 보여주고, 언제 바꾸게 하고, 실패를 어떻게 알리는가"를 분해합니다.

# 1. Tab Toggle

대표: MakeCode

```
[ Blocks ] [ JavaScript ] [ Python ]
```

## 장점
- 화면이 단순
- 한 표현에 집중
- 작은 태블릿에서도 유리
- 각 editor가 전체 공간 사용

## 단점
- 두 표현을 동시에 눈으로 비교하기 어려움
- 전환 직전/직후 대응 위치를 잃기 쉬움

## 개선
전환 직후:
- 이전 cursor 위치에 대응하는 block highlight
- 변환된 line highlight
- "3개 표현이 변경됨" diff badge

가 있으면 좋습니다.

---

# 2. Split View

대표: BlockPy / BlockMirror, EduBlocks의 block→text 학습 경험

```
┌──────── Blocks ────────┬────── Python ──────┐
│                        │                     │
│                        │                     │
└────────────────────────┴─────────────────────┘
```

## 장점
- 직접 비교 학습에 가장 좋음
- 한쪽 edit가 다른 쪽에 미치는 영향 즉시 확인
- 대응 syntax를 찾기 쉬움

## 단점
- 작은 화면에서 매우 좁음
- 양쪽 scroll/cursor를 동기화하지 않으면 오히려 혼란
- realtime reparse 비용이 큼

## 추천
- desktop: 55:45 또는 60:40
- tablet portrait: 상하
- mobile: toggle + mini preview
- linked scrolling은 강제보다 선택형이 좋음

---

# 3. Generated Code Preview

대표: mBlock, Tinkercad Blocks + Text

블록이 정본이고 텍스트는 generated output입니다.

## 장점
- 구현이 안정적
- 학생이 syntax를 볼 수 있음
- block project를 깨뜨리지 않음

## 단점
- 학생이 text를 "실제 편집 가능한 코드"로 착각할 수 있음
- editable로 바꾸는 순간 canonical state 문제가 생김

## UI에서 반드시 구분
- **Generated preview**
- **Editable code**

를 색상/헤더/lock icon으로 명확히 분리해야 합니다.

---

# 4. Graduated Mode

대표: Tinkercad

```
Blocks → Blocks + Text → Text
```

## 장점
학습 전환 단계를 명확히 보여줍니다.

## 위험
Text 전환이 irreversible하면 학생이 실수로 project representation을 바꿀 수 있습니다.

## 추천 modal
```
텍스트 편집 모드로 전환하면 현재 블록으로 다시 완전히
복원되지 않을 수 있습니다.

[취소] [사본을 만들고 전환]
```

원본을 자동 snapshot하는 게 더 안전합니다.

---

# 5. Hybrid Text Island

대표: VEX Switch

```
repeat 10 times
  ├─ [ drive forward ]
  ├─ [ Python: motor.spin(FORWARD) ]
  └─ [ wait 1 sec ]
```

## 장점
- 학생이 원하는 부분만 text화
- visual nesting 유지
- syntax 부담을 단계적으로 증가

## 가장 흥미로운 확장
text island가 parser에 의해 native blocks로 변환 가능하면:

```
text island
   ↓ parse success
native blocks

text island
   ↓ parse fail
keep raw text + diagnostics
```

가 됩니다.

이는 VEX + BlockMirror의 결합 형태입니다.

---

# 6. Syntax-rich Blocks

대표: Pencil Code / Droplet

블록이 자연어 번역이 아니라 실제 source syntax를 많이 노출합니다.

## 장점
- text 전환 시 낯섦 감소
- block/text가 같은 언어라는 mental model 강화

## 단점
- 어린 학습자에게 punctuation이 너무 빨리 노출될 수 있음

## 추천 progressive label
초기:
```
10번 반복
```

중간:
```
for 10 times
```

전환:
```
for i in range(10):
```

같은 representation density 단계도 가능합니다.

---

# 7. Conversion Failure UX

양방향 시스템에서 가장 중요한데 자주 빠지는 부분입니다.

## 나쁜 UX
- 버튼 눌렀는데 아무 일 없음
- 일부 코드가 조용히 사라짐
- "Cannot convert" 한 줄

## 좋은 UX

```
블록으로 바꾸지 못한 코드가 2곳 있습니다.

12행: list comprehension
18행: lambda expression

이 코드는 '텍스트 블록'으로 보존됩니다.
[블록으로 보기] [텍스트 유지]
```

### 원칙
- 실패 위치
- 이유
- 데이터가 보존되는지
- 실행 가능한지
- 다시 text로 갈 수 있는지

를 모두 알려야 합니다.

---

# 8. Correspondence Highlight

가장 강하게 추천하는 기능입니다.

### block hover
대응 text range highlight.

### text cursor
대응 block outline.

### execution
양쪽 모두 같은 statement를 highlight.

### error
text line과 block에 같은 diagnostic ID를 연결.

이를 위해 source map이 필요합니다.

---

# 9. Diff on Conversion

MakeCode식 decompile은 같은 의미라도 code shape가 바뀔 수 있습니다.

전환 후 자동 diff:

```
변환하면서 다음이 정리되었습니다.
- for loop 표현 변경
- 변수 이름 유지
- 주석 1개 위치 조정
- 1개 문장은 텍스트 블록으로 보존
```

학생뿐 아니라 교사/개발자 debugging에도 매우 유용합니다.

---

# 10. Undo/Redo 경계

두 editor가 각자 undo stack을 가지면 문제가 생깁니다.

예:
1. text 수정
2. block 변환
3. block 이동
4. Ctrl+Z

이때 text 수정 전으로 갈지, block 이동만 취소할지 불분명합니다.

## 추천
project-level command log:

```ts
type EditCommand =
  | TextEdit
  | BlockMutation
  | RepresentationSwitch
  | ConversionFallback;
```

을 두고 representation 전환도 undoable event로 취급합니다.

---

# 11. 초등 교육에서의 추천 모드

## 1단계
Blocks only

## 2단계
Blocks + read-only code preview

## 3단계
Split view + correspondence highlight

## 4단계
Hybrid text islands

## 5단계
Text editor + block reconstruction button

이 순서는 학생에게 갑자기 모든 syntax 책임을 넘기지 않습니다.

# 핵심 결론

좋은 block↔text UX의 목적은 **"버튼을 눌러 변환하는 것"이 아니라 "두 표현이 같은 프로그램이라는 것을 학생이 인식하게 하는 것"**입니다.

따라서 가장 중요한 UI는 변환 버튼보다:
- 대응 highlight
- 단계적 text exposure
- raw text fallback
- conversion diff
- 안전한 snapshot
입니다.
