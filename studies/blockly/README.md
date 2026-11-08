# Blockly — 범용 블록 편집 엔진 기준점

> 기준일: 2026-11-08  
> 소스 기준: RaspberryPiFoundation/blockly @ 7833379192e623086f3767ec3835b9acfd49e43c  
> 분석 깊이: L2 + 일부 L3  
> 한 줄 판정: **Blockly는 블록 상태 편집·직렬화·블록→텍스트 생성 프레임워크이지, 임의의 텍스트→블록 역변환 엔진이 아니다.**

## 1. 왜 별도 서비스급 분석 대상으로 두는가

기존 문서에서는 Blockly를 ‘기준점’으로만 다뤘지만, 실제 교육용 코딩 서비스 상당수가 Blockly 계열 위에 자체 parser, decompiler, runtime, 저장 계층을 얹는다. 따라서 Blockly 자체가 제공하는 기능과 제품이 추가 구현한 기능을 분리해야 양방향 변환을 정확히 판정할 수 있다.

현재 공식 저장소는 Raspberry Pi Foundation 조직 아래에 있다. 2025년 11월 stewardship 이전 이후 저장소와 개발은 RaspberryPiFoundation/blockly에서 이어지고 있다.

## 2. 제품/라이브러리 구조

Blockly 자체는 완성형 학습 서비스가 아니라 웹 애플리케이션에 시각적 블록 편집기를 삽입하는 라이브러리다. 분석할 때 ‘홈/학급/공유’ 같은 제품 화면을 억지로 대응시키지 않고 아래 계층으로 본다.

| 계층 | 역할 |
|---|---|
| Workspace | 블록 그래프와 편집 상태 |
| Blockly UI | toolbox, flyout, field, connection, gesture, keyboard/accessibility |
| Serialization | workspace 상태 저장·복원 |
| CodeGenerator | block graph를 대상 언어 문자열로 생성 |
| Plugins | field, renderer, serializer, workspace 기능 확장 |
| Host app | 실행, 저장 서버, 계정, 텍스트 에디터, parser/decompiler 등을 별도 구현 |

## 3. 데이터 흐름

### 블록 상태 보존

Workspace → serialization.workspaces.save → JSON state → load → Workspace

이 흐름은 **블록 상태의 저장/복원**이다. JavaScript나 Python 텍스트를 다시 블록으로 파싱하는 흐름과 다르다.

### 코드 생성

Workspace → block별 generator → CodeGenerator.workspaceToCode → source string

즉 기본 방향은 block graph → text다. 생성된 문자열을 host app에서 보여주거나 실행할 수 있지만, 그 문자열을 다시 native Blockly workspace로 복원하는 범용 parser는 별도 구현해야 한다.

## 4. UI/UX 관점

Blockly의 직접 UI는 workspace 내부에 집중된다.

- toolbox / flyout 탐색
- drag-and-drop 연결
- field 입력과 dropdown
- zoom, pan, trashcan
- context menu
- keyboard navigation 및 접근성
- undo/redo를 위한 event 모델
- workspace serialization

반면 다음은 Blockly 자체 기능으로 간주하면 안 된다.

- 로그인/프로젝트 목록
- 텍스트 IDE
- 동일 프로젝트의 block↔Python 전환
- simulator/hardware runtime
- 자동 저장 서버
- 학급/과제 관리

이들은 Blockly를 사용하는 제품이 별도 설계하는 계층이다.

## 5. 양방향 판정

| 질문 | 판정 |
|---|---|
| 블록→텍스트 생성 | O |
| 블록 workspace 저장/복원 | O |
| 임의 JavaScript/Python→native blocks | **X — 기본 제공 아님** |
| 지원 문법 subset decompiler | host app이 구현해야 함 |
| 같은 프로그램의 실질 왕복 | Blockly 단독으로는 해당 없음 |

따라서 Blockly 사용 여부만으로 제품을 ‘양방향’으로 분류해서는 안 된다.

## 6. 얽힌 문제를 어떻게 풀고 있는가

### 편집 상태와 실행 의미를 분리
블록 위치, 접힘, mutator 상태, ID 같은 편집 정보는 serialization이 담당한다. 텍스트 생성기는 실행 가능한 source string 생성에 집중한다.

### 언어별 generator 분리
언어마다 block generator를 등록하므로, 같은 workspace에서 여러 언어 출력을 만들 수 있다. 그러나 이 다중 출력성은 역변환을 의미하지 않는다.

### 이벤트 기반 확장
workspace change event를 host app이 구독해 저장, 실행, 코드 미리보기, VM 동기화 등을 구현할 수 있다. Scratch 같은 제품은 바로 이 seam을 활용해 자체 VM 상태와 연결한다.

## 7. 우리 설계에 가져갈 것

1. **workspace serialization과 semantic source/IR을 같은 데이터로 취급하지 않는다.**
2. block→text generator와 text→block parser/decompiler를 독립 모듈로 둔다.
3. 변환 실패 시 원문을 버리지 않도록 raw/unsupported 표현을 별도 설계한다.
4. host app의 실행·저장·전환 기능을 Blockly 기능처럼 설명하지 않는다.
5. 접근성·키보드 조작은 editor core 수준 요구사항으로 둔다.

## 8. 증거 원장 요약

| 주장 | 상태 | 근거 | 신뢰도 |
|---|---|---|---|
| workspace serialization API 존재 | 직접 확인 | core/serialization.ts, 공식 guide | A |
| CodeGenerator.workspaceToCode 존재 | 직접 확인 | core/generator.ts | A |
| 기본 범용 text→blocks parser 없음 | 소스/공식 기능 경계 확인 | generator·serialization 구조 및 공식 문서 | A |
| 현재 stewardship가 Raspberry Pi Foundation | 공식 현행 저장소 확인 | RaspberryPiFoundation/blockly | A |

## 9. 다음 L3/L4 검증

- workspace event → serialization → undo stack의 경계 추적
- custom mutator의 extra state 저장/복원 회귀 테스트
- 대형 workspace에서 code generation/serialization 성능 측정
- keyboard-only와 screen reader 사용성 실험
- host app에서 text parser를 붙일 때 source mapping 전략 비교

## Sources

- https://github.com/RaspberryPiFoundation/blockly
- https://developers.google.com/blockly/guides/configure/serialization
- https://developers.google.com/blockly/guides/create-custom-blocks/code-generation/overview
- 상세 파일 지도: [SOURCE-MAP.md](./SOURCE-MAP.md)
