# EduBlocks

> 조사 기준일: 2026-11-08  
> 판정: **현재 공개 근거상 block→text 중심의 editable one-way transition으로 보는 것이 안전**  
> 신뢰도: **B/C** — 현재 공식 문서 + 기존 연구 교차. reverse conversion은 현재 공개 자료에서 확인하지 못함.

## 1. 현재 제품

EduBlocks는 현재 Anaconda 계열의 무료 교육 도구로 안내되고 있으며 다음 project type을 제공합니다.

- Python 3
- HTML
- micro:bit
- CircuitPython
- Raspberry Pi

교사용 Classroom 기능도 제공합니다.

## 2. 핵심 UX 문구

공식 문서의 핵심 표현은:

- "Blocks to Text made easy"
- 각 block이 한 줄의 code를 표현
- block을 workspace에 끌어올 때 text editor가 realtime으로 갱신

입니다.

즉 학습 목표가 **블록의 의미를 실제 syntax 한 줄과 직접 연결**하는 데 있습니다.

## 3. 중요한 판정 주의

"block과 text를 함께 보여준다"는 것만으로 dual-modality라고 부르면 안 됩니다.

2023년 K-12 data science tool 비교 연구는 EduBlocks를:

- text version을 볼 수 있음
- text를 편집할 수도 있음
- 그러나 text edit가 다시 block으로 변환되지는 않음

이라는 **editable one-way transition**으로 분류했습니다.

2026 현재 공식 문서에서는 block→text realtime update는 여전히 명시하지만 text→block 역변환을 명시하지 않습니다.

따라서 이 저장소에서는 **현재 직접 reverse 동작이 확인되기 전까지 S2**로 분류합니다.

## 4. 교육적 설계의 강점

### 1 block ≈ 1 line
초보 학습에서 매우 강력합니다.

Scratch식 의미 block:
```
10번 반복하기
```

와 달리 EduBlocks는 "이 block이 실제 Python에서 어떤 한 줄인가"를 직접 보여주려 합니다.

### 실시간 나란히 보기
블록을 옮길 때 text가 바로 변하면:
- indentation
- function call
- argument
- variable assignment

의 관계를 눈으로 연결하기 쉽습니다.

### Python으로 넘어갈 명시적 목적
제품 브랜딩 자체가 block-only creativity보다 **text language learning**에 더 집중합니다.

## 5. 기술적으로 추정 가능한 구조

공개 core implementation은 현재 쉽게 확인되지 않아 내부 구조를 단정하면 안 됩니다.

다만 확인된 UX상 최소한 다음 경로는 존재합니다.

```
Block workspace
  ↓
language-specific code generation
  ↓
live text editor/view
```

반대 방향 parser/decompiler는 공개 근거가 없습니다.

## 6. 장점

- syntax correspondence가 매우 명료
- Python뿐 아니라 HTML/hardware context로 확장
- classroom workflow가 있음
- block→text transition 목적이 명시적
- 초등 고학년~중등 Python 전환 수업에 설명하기 쉬움

## 7. 한계

### one-way cliff
학생이 text를 본 뒤 자유롭게 고치기 시작하면 block representation이 더 이상 정본이 아닐 수 있습니다.

### 두 상태가 갈라지는 순간
Text editor가 정말 editable하다면 UX에서 반드시:
- 이제 block으로 돌아갈 수 없음
- 어떤 쪽이 실행되는가
- 어느 쪽이 저장되는가

를 명확히 해야 합니다.

### mapping이 너무 1:1이면
초보에게는 좋지만 Python의:
- expression nesting
- comprehension
- decorators
- context manager
- advanced function syntax

로 갈수록 block 모델이 급격히 복잡해질 수 있습니다.

## 8. 우리가 가져갈 것

양방향 엔진을 만들더라도 **1 block ↔ 1 line 대응성**은 UX에 적극 반영할 가치가 있습니다.

예:
- block을 hover하면 대응 text line highlight
- text line hover하면 block highlight
- "이 블록은 Python으로 이렇게 씁니다" 항상 노출
- block label을 실제 API/function name과 너무 멀게 번역하지 않기

## 9. 추가 검증 과제

향후 브라우저 직접 interaction으로 확인할 것:
- text를 고친 직후 block side가 바뀌는지
- syntax error text 입력 시 block side 처리
- text only mode → blocks 복귀 가능 여부
- comments round-trip
- nested expressions
- project save 시 canonical representation

## Sources

- EduBlocks  
  https://edublocks.org/docs/
- Current docs  
  https://docs.edublocks.org/
- Current editor  
  https://app.edublocks.org/
- Israel-Fishelson et al. (2023)  
  https://doi.org/10.1080/0144929X.2023.2295956
