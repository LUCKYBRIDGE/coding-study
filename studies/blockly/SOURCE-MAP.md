# Blockly 소스 지도

> 저장소: https://github.com/RaspberryPiFoundation/blockly  
> 검토 커밋: 7833379192e623086f3767ec3835b9acfd49e43c  
> 목적: **Blockly 자체가 제공하는 블록 상태·생성 기능과 제품이 별도 구현해야 하는 역변환 기능을 구분**한다.

## 핵심 경로

| 경로 | 분석상 의미 |
|---|---|
| packages/blockly/core/generator.ts | CodeGenerator와 workspaceToCode의 핵심 |
| packages/blockly/core/serialization.ts | serialization 공개 진입점 |
| packages/blockly/core/serialization/registry.ts | serializer 등록 구조 |
| packages/blockly/core/blockly.ts | core export와 주요 API 연결 |
| packages/blockly/generators/ | 대상 언어별 code generator |
| packages/plugins/sample-app/src/index.js | host app이 workspace→code를 호출하는 예 |
| packages/plugins/sample-app/src/serialization.js | localStorage에 workspace state를 저장/복원하는 예 |
| packages/docs/docs/guides/configure/serialization.mdx | 공식 serialization 사용 설명 |
| packages/docs/docs/guides/app-integration/run-code.mdx | 생성 코드를 host app에서 사용하는 흐름 |

## 확인한 함수/호출

### CodeGenerator.workspaceToCode
packages/blockly/core/generator.ts에서 workspace를 받아 생성된 source string을 반환하는 API가 확인된다.

### serialization.workspaces.save/load
sample app과 공식 문서에서 workspace state를 JSON으로 저장하고 다시 load하는 패턴이 확인된다. 이 API는 source text parser가 아니다.

## 구조 해석

    Blockly workspace
      ├─ serialization → JSON block/editor state
      └─ CodeGenerator → JavaScript/Python/... source string

    arbitrary source text
      └─ [범용 parser/decompiler 없음]
          └─ 제품이 별도 계층을 구현해야 함

## L3에서 더 추적할 지점

1. event class와 undo/redo stack
2. mutator extra state serializer
3. generator precedence와 block별 forBlock registry
4. workspace load 시 event disable/enable 경계
5. plugin이 core state에 추가 정보를 붙이는 방식

## 주의

- repository 내부의 sample app은 **Blockly를 사용하는 예제 host**이지 Blockly가 학급·프로젝트·런타임 기능을 모두 제공한다는 뜻이 아니다.
- serialization load를 text→block 역변환으로 표기하지 않는다.
- 과거 google/blockly 링크가 보이더라도 2026-11 기준 현행 저장소는 RaspberryPiFoundation/blockly이다.
