# 소스코드 파일별 연결 지도 — blockpy-edu/BlockMirror

> 근거: [확인한 Git 트리](https://github.com/blockpy-edu/BlockMirror/tree/9b77174b297c60bed07992d40fc9a00885291e1c) · commit `9b77174b297c60bed07992d40fc9a00885291e1c`  
> 주의: 저장소 실제 파일명과 조사 목적 설명을 연결한 **소스 탐색 지도**이며 모든 함수 호출 관계를 정적 분석·실행 검증한 결과는 아님.

## 대표 모듈/페이지 구성

### 공유 상태/화면

- [`src/block_mirror.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/block_mirror.js)
- [`src/block_editor.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/block_editor.js)
- [`src/text_editor.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/text_editor.js)

### 파싱/변환

- [`src/text_to_blocks.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/text_to_blocks.js)
- [`src/skulpt/parser.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/skulpt/parser.js)
- [`src/skulpt/ast.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/skulpt/ast.js)

### 문법별 노드

- [`src/ast/ast_Assign.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/ast/ast_Assign.js)
- [`src/ast/ast_If.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/ast/ast_If.js)
- [`src/ast/ast_For.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/ast/ast_For.js)
- [`src/ast/ast_FunctionDef.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/ast/ast_FunctionDef.js)
- [`src/ast/ast_Comment.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/ast/ast_Comment.js)
- [`src/ast/ast_Raw.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/ast/ast_Raw.js)

### 팔레트/번들

- [`src/toolbars.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/toolbars.js)
- [`src/blockly_shims.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/blockly_shims.js)
- [`webpack.config.js`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/webpack.config.js)

### 검증

- [`test/simple_dev.html`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/test/simple_dev.html)
- [`test/simple_prod.html`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/test/simple_prod.html)
- [`README.md`](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/README.md)

## UI 이벤트와 데이터 흐름

CodeMirror(text) → 공유 code_ → Sk.parse/AST → TextToBlocks XML → Blockly workspace. 블록 변경 → Python generator workspaceToCode → 공유 code_ → CodeMirror. silence flags로 왕복 이벤트 억제.

## 소스에서 실재를 확인할 수 있는 기술적 쟁점

- **표시 상태:** 각 화면 컴포넌트 또는 컨트롤러는 어떤 editor와 toolbars를 그리는가
- **사용자 액션:** 이벤트가 어느 모델을 먼저 갱신하는가
- **변환 단계:** generator·parser·decompiler·visitor 중 실제 어느 경로를 타는가
- **오류 반환:** parse failure, generator failure, device error는 서로 다른가
- **영속화:** 저장하는 정본(source-of-truth)과 화면 상태가 구분되는가
- **Undo/Redo:** 소스 편집과 시각 블록 편집이 어떤 history를 사용하는가
- **연관 테스트:** 같은 오류를 다시 발생시키는 fixture가 있는가

## 내부적으로 얽힌 문제를 조사할 때의 순서

1. 실제 사용자 이벤트가 발생하는 파일을 찾는다.
2. 이벤트가 어떤 공유 모델·상태를 변경하는지 확인한다.
3. 모델에서 generator/parser/decompiler 호출 경로를 따라간다.
4. 성공/오류/부분 실패 분기와 원문 보존 방식을 확인한다.
5. UI에 결과가 다시 반영되는 이벤트와 저장·Undo 경계를 추적한다.
6. 모든 주장을 구체 파일·함수·테스트와 연결한다.

## 검증 한계

이 저장소는 BlockMirror 라이브러리이고 학급 관리/과제/성적과 같은 BlockPy 서비스 상위 기능을 포함한다고 볼 수 없음.

### 다음 단계의 검증 작업

- 각 핵심 파일의 실제 함수·클래스와 호출 위치를 줄 번호 단위로 추출
- 화면별 버튼 event handler ↔ 내부 action 매핑
- 코드 전환 전후 스냅샷 및 회귀 테스트 실행
- 로그인·공유·저장·미리보기의 request/response 경로 분리
