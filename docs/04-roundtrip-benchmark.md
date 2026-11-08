# 블록 ↔ 텍스트 Round-trip Benchmark

> 목적: 특정 서비스/엔진이 "양방향"이라고 주장할 때 감으로 판단하지 않고 같은 테스트로 비교

# 1. 판정 함수

## Text round-trip

```
T0
↓ textToBlocks
B1
↓ blocksToText
T1
```

검사:

```
parse(T0).semantics == parse(T1).semantics
```

문자열이 완전히 같을 필요는 없습니다.

별도로:
- formatting preservation
- comment preservation
- identifier preservation
도 측정합니다.

## Block round-trip

```
B0
↓ blocksToText
T1
↓ textToBlocks
B1
```

검사:
- semantic block tree
- block type
- field values
- nesting
- comments
- custom mutation
- x/y
- IDs

을 수준별로 비교합니다.

# 2. 필수 테스트 세트

## A. 기본 statements
```python
x = 10
print(x)
```

## B. arithmetic precedence
```python
x = 1 + 2 * 3
y = (1 + 2) * 3
```

괄호가 사라져 의미가 바뀌지 않는지 확인.

## C. if / elif / else
```python
if x > 10:
    print("big")
elif x == 10:
    print("same")
else:
    print("small")
```

## D. nested loops
```python
for i in range(3):
    for j in range(2):
        print(i, j)
```

## E. while + break/continue

## F. functions
- no parameter
- multiple parameters
- return
- recursion
- default arguments

## G. scope
- global
- local
- shadowing

## H. collections
- list
- dict
- indexing
- slicing

## I. strings
- quotes
- escaped quotes
- newline
- unicode/Korean

## J. comments
- line comment
- comment before statement
- trailing comment
- multiple consecutive comments

## K. blank lines / formatting
실행 의미는 없지만 text fidelity 측정.

## L. incomplete source
학생이 typing 중인 상태:

```python
if x >
```

이때:
- editor crash?
- block pane disappears?
- previous blocks retained?
- raw text block?
- diagnostic only?

## M. unsupported syntax
예:
```python
result = [x*x for x in values if x > 0]
```

처리:
- reject
- raw block
- grey block
- partial conversion
- silent loss

중 무엇인지 기록.

## N. external library
```python
import random
print(random.randint(1, 6))
```

## O. custom API / hardware
```
motor.run(...)
sensor.read(...)
```

API metadata가 block mapping에 어떤 영향을 주는지 확인.

# 3. UX 테스트

## Switch latency
- 50 blocks
- 200 blocks
- 500 blocks
- 1000 blocks

각각 변환 지연 측정.

## Cursor continuity
text 20행에 cursor가 있을 때 blocks로 전환하면 대응 block으로 이동하는가?

## Scroll continuity
전환 후 같은 문맥이 화면 중앙에 남는가?

## Selection
여러 block 선택 ↔ source range mapping.

## Errors
syntax error가 있는 상태에서 전환 버튼 behavior.

## Undo
전환 직후 Undo가 representation 전환을 되돌리는가?

# 4. Fidelity score

각 항목 0~2점.

| 항목 | 0 | 1 | 2 |
|---|---|---|---|
| 실행 의미 | 깨짐 | 일부 제한 | 보존 |
| nesting | 손실 | 일부 | 보존 |
| identifiers | 변경 | 일부 | 보존 |
| comments | 손실 | 위치 변화 | 보존 |
| formatting | 재생성 | 부분 | 보존 |
| unsupported | 삭제 | 변환 차단 | raw 보존 |
| block metadata | 손실 | 일부 | 보존 |
| UI state | 손실 | 일부 | 보존 |

총점은 시스템 비교를 위한 내부 지표이며 "교육적으로 좋다"와 동일하지 않습니다.

# 5. Pedagogy score

| 항목 | 질문 |
|---|---|
| Correspondence | 같은 statement의 두 표현이 연결되어 보이는가? |
| Gradual transition | 한 번에 모든 syntax를 요구하지 않는가? |
| Error safety | 실패해도 학생 작업을 잃지 않는가? |
| Explanatory feedback | 왜 변환 안 되는지 설명하는가? |
| Reconstruction | text를 보고 blocks를 다시 구성하는 학습이 가능한가? |
| Fade scaffold | 시간이 지나면서 block 도움을 줄일 수 있는가? |

# 6. 자동화 테스트 권장

## Golden tests
입력 source와 예상 block JSON snapshot.

## Property tests
무작위 AST를 생성하고 왕복 semantics 비교.

## Differential tests
두 경로가 같은 semantic IR을 만드는지 비교.

예:

```
Python → IR
Blocks → IR
```

## Regression corpus
사용자가 실제로 변환에 실패한 code를 익명화하여 test fixture에 계속 추가.

# 7. 테스트 결과 기록 포맷

```yaml
platform: example
version: 1.0
date: 2026-11-08
case: nested-if-001
text_to_blocks:
  success: true
  unsupported: []
blocks_to_text:
  success: true
semantic_equal: true
comments_preserved: false
format_preserved: false
block_ids_preserved: false
latency_ms: 42
notes: ""
```

이 형식으로 향후 실제 브라우저 실험 결과를 누적할 수 있습니다.
