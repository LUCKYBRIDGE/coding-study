# Pencil Code

> 조사 기준일: 2026-11-08  
> 판정: **text-first 교육용 dual-mode editor의 가장 순수한 사례 중 하나**  
> 기반: **Droplet**  
> 신뢰도: **A**

## 1. 제품 성격

Pencil Code 공식 사이트는 현재도:

> professional programming languages를 blocks 또는 text로 작업할 수 있는 editor

라고 설명합니다.

지원 언어로:
- CoffeeScript
- JavaScript
- CSS
- HTML

을 표시합니다.

주력 학습 언어는 CoffeeScript 계열입니다.

## 2. 핵심 UX

Pencil Code의 중요한 점은 "블록을 배우고 나중에 완전히 다른 텍스트 IDE로 이동"하는 구조가 아니라는 것입니다.

같은 document에서:
- blocks
- text

를 토글합니다.

교사용 자료도 text에서 코드를 추가/수정한 뒤 다시 block mode로 돌아가 그 코드가 어떻게 blocks로 보이는지 확인하는 흐름을 설명합니다.

## 3. 내부 구조

공개 저장소:
`PencilCode/pencilcode`

에서:
- Droplet editor dependency
- `content/src/view.js`의 droplet 사용
- Droplet CSS
- block/text toggle UI

를 확인할 수 있습니다.

Pencil Code가 양방향 변환 엔진을 따로 새로 만든 것이 아니라 **Droplet의 text-preserving structural editor를 실제 교육 서비스에 통합**한 형태입니다.

## 4. 왜 CoffeeScript가 잘 맞나

CoffeeScript는:
- indentation
- 간결한 syntax
- 적은 punctuation

때문에 puzzle block과 텍스트 표현 사이의 시각적 거리가 상대적으로 짧습니다.

예를 들어 초보자가 block 안의 text를 보고 그대로 textual code와 연결하기 쉽습니다.

이 전략은 "Python을 블록화"할 때도 시사점이 있습니다.

> 최종 텍스트 언어와 block label의 표면 형태가 가까울수록 전환 비용이 낮아진다.

## 5. UI 철학

### 한 프로그램, 두 조작 방식
block mode를 별도 초급 언어로 취급하지 않습니다.

### block 내부에 실제 syntax 노출
Scratch처럼 자연어 명령만 보여주는 것보다 텍스트 전환을 명시적으로 준비합니다.

### toggle이 핵심 affordance
학생이 필요할 때 즉시 양쪽 표현을 비교할 수 있습니다.

## 6. 저장/공유 측면

Pencil Code는 웹 기반 학습 공간이며 public sharing을 강하게 전제로 합니다.

현재 사이트 자체도:
- 게시된 데이터가 public
- 데이터 안전성에 의존하지 말 것
- 개인정보를 올리지 말 것

이라는 주의를 명시합니다.

학교용으로 그대로 복제한다면 이 보안/개인정보 모델은 적합하지 않을 수 있습니다.

## 7. 장점

- text syntax와 block의 직접 대응성이 좋음
- same-document toggle
- text fidelity가 높음
- 학습 전환 목표가 제품 구조에 내장됨
- Droplet 오픈소스를 통해 구현을 추적 가능

## 8. 약점

### 현대적인 IDE 기능은 제한적
Monaco 계열 최신 IDE만큼:
- autocomplete
- refactoring
- type-aware diagnostics
- language server

가 강한 구조는 아닙니다.

### 대형 프로그램 확장
Droplet은 educational program 규모에는 매우 적합하지만 대규모 general-purpose IDE 아키텍처로는 별도 검토가 필요합니다.

### 서비스 UX의 시대감
핵심 아이디어는 강하지만 UI 전반은 최신 교육 앱과 비교해 현대화 여지가 큽니다.

## 9. 유지보수 신호

Pencil Code 공개 저장소는 2026년에도 commit이 확인되지만 활동 빈도는 높지 않은 편입니다.

따라서:
- **아키텍처 참고 가치: 높음**
- 새 프로젝트의 전체 stack을 그대로 채택: 신중

으로 보는 게 적절합니다.

## 10. 우리가 가져갈 요소

### 좋은 것
- block text를 실제 syntax와 가깝게
- one-click block/text toggle
- 동일 cursor/문맥 유지
- 학생이 text를 고친 뒤 block에서 다시 구조를 보게 하기
- 짧은 program에서 representation comparison을 반복하게 하기

### 개선해서 가져갈 것
- Monaco/modern editor
- explicit conversion diagnostics
- diff highlight
- unsupported syntax 표시
- private classroom storage
- teacher analytics

## Sources

- Pencil Code  
  https://pencilcode.net/
- Pencil Code GitHub  
  https://github.com/PencilCode/pencilcode
- Droplet  
  https://github.com/droplet-editor/droplet
- Droplet architecture  
  https://github.com/droplet-editor/droplet/blob/master/DESIGN.md
