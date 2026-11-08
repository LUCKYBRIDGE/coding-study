# Code.org App Lab / Game Lab + Droplet

> 조사 기준일: 2026-11-08  
> 판정: **JavaScript text와 block-like 편집을 같은 원문 위에서 다루는 양방향 구조**  
> 왕복 수준: **S3~S4, text fidelity 강점**  
> 신뢰도: **A**

## 1. 현재 제품 위치

Code.org의 현재 지원 문서는:

- App Lab: JavaScript를 **typing 또는 droplet blocks**로 작성
- Game Lab: JavaScript 기반

이라고 설명합니다.

Code.org 내부에는 Sprite Lab 등 Blockly 계열 제품도 있지만, App Lab/Game Lab의 전통적인 JavaScript block/text 경험은 **Droplet** 계열입니다.

즉 "Code.org = 전부 Blockly"로 분석하면 틀립니다.

## 2. 공개 저장소에서 확인되는 것

`code-dot-org/code-dot-org`에 현재도 다음 계열 파일이 존재합니다.

- App Lab droplet configuration
- Game Lab / p5 lab droplet configuration
- droplet utility
- palette / command mapping

이 configuration은 JavaScript 함수/API를 어떤 block 형태와 palette category로 보여줄지 정의합니다.

## 3. Droplet이 일반 Blockly와 다른 점

Blockly 기본:

```
Block AST → generator → source string
```

Droplet:

```
Source text → parser → source ranges
            ↘ block/socket/indent markup
```

블록 화면은 **새로운 프로그램 표현을 생성한 별도 모델이라기보다 원래 텍스트에 구조적 UI를 씌운 것**에 가깝습니다.

## 4. Droplet 내부 데이터 모델

공개 `DESIGN.md`에서 document는 linked-list token으로 구성됩니다.

- documentStart / documentEnd
- blockStart / blockEnd
- socketStart / socketEnd
- indentStart / indentEnd
- text
- newline

모든 원문 문자(일부 leading whitespace 제외)는 text token에 들어갑니다.

다시 source text를 얻을 때:
- markup token 제거
- text token 연결
- indent 복원

방식입니다.

### 결과
블록에서 text로 바꿀 때 generator가 원문을 재작성하는 게 아니라 **원문이 애초에 살아 있습니다.**

## 5. parser의 역할

Droplet parser는 언어 AST의 source location을 이용하여:

- 이 범위는 Block
- 이 범위는 Socket
- 이 범위는 Indent

라고 표시합니다.

JavaScript parser adapter는 AST node의 bounds를 block bounds로 변환합니다.

따라서 시각 표현은 AST 의미 + 실제 source range 양쪽을 사용합니다.

## 6. block 편집도 text mutation

블록을 drag/drop하면 model의 token list를:
- insert
- remove
- replace

합니다.

그 과정에서 whitespace와 source location tracking을 함께 처리합니다.

이 점이 "block AST를 편집한 다음 text를 다시 생성"하는 Blockly 모델과 근본적으로 다릅니다.

## 7. Code.org에 특히 잘 맞는 이유

App Lab/Game Lab은:
- 학생이 block으로 시작
- 같은 JavaScript 문법을 text로 봄
- 특정 교육 API 호출이 palette block으로 보임

이라는 전환이 중요합니다.

Droplet은 block 자체가 실제 source text와 매우 가깝기 때문에 **블록→텍스트 전환에서 학생이 갑자기 전혀 다른 코드를 보는 문제를 줄입니다.**

## 8. 장점

### text fidelity
사용자가 직접 쓴 표현을 generator가 임의 포맷하지 않음.

### 같은 editor에서 dual mode
두 개의 독립 모델을 억지로 동기화하지 않아도 됨.

### mobile/touch 친화 아이디어
Droplet 자체 README는 키보드 입력이 불편한 모바일 환경도 동기로 언급합니다.

### 교육적 대응성
visual block과 textual syntax가 거의 동일한 위치·어휘 관계를 가질 수 있음.

## 9. 단점

### 언어 adapter 비용
각 언어 parser에서 source location과 block/socket 규칙을 설계해야 합니다.

### 복잡한 structural editor
selection, cursor, drag/drop, whitespace, undo 위치까지 text token과 맞춰야 합니다.

### Blockly 생태계 재사용성이 낮음
기존 Blockly plugin/toolbox 자산을 그대로 쓰는 구조가 아닙니다.

### semantic block abstraction에는 약할 수 있음
블록을 실제 언어 syntax보다 훨씬 고수준 DSL로 만들고 싶다면 text-overlay 접근보다 compiler/IR 방식이 자연스럽습니다.

## 10. 우리 설계에 가져갈 것

텍스트 코딩을 최종 목표로 삼는 교육 도구라면 다음 철학이 매우 좋습니다.

> "블록을 별도 언어로 만들지 말고 실제 코드의 구조적 편집 인터페이스로 만든다."

특히:
- block label을 실제 syntax와 가깝게
- block mode에서도 variable/function name을 실제 text 그대로
- text transition 시 코드가 갑자기 재포맷되지 않도록
- cursor/source location 공유

가 유용합니다.

## Sources

### Product
- Code.org App Lab/Game Lab differences  
  https://support.code.org/hc/en-us/articles/360039984911-What-are-the-differences-between-App-Lab-Game-Lab-Web-Lab-Sprite-Lab-and-Java-Lab
- Code.org tools  
  https://code.org/en-US/tools

### Source
- Code.org repository  
  https://github.com/code-dot-org/code-dot-org
- Droplet repository  
  https://github.com/droplet-editor/droplet
- Droplet architecture  
  https://github.com/droplet-editor/droplet/blob/master/DESIGN.md
- Droplet README / parser extension model  
  https://github.com/droplet-editor/droplet/blob/master/README.md
