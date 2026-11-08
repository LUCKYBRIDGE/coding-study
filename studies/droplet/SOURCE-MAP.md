# 소스코드 파일별 연결 지도 — droplet-editor/droplet

> 근거: [확인한 Git 트리](https://github.com/droplet-editor/droplet/tree/bc724da8880b278388051e5f0f7f2ddd36e005fc) · commit `bc724da8880b278388051e5f0f7f2ddd36e005fc`  
> 주의: 저장소 실제 파일명과 조사 목적 설명을 연결한 **소스 탐색 지도**이며 모든 함수 호출 관계를 정적 분석·실행 검증한 결과는 아님.

## 대표 모듈/페이지 구성

### 화면/API

- [`src/controller.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/controller.coffee)
- [`src/main.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/main.coffee)
- [`example/example.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/example/example.coffee)

### 모델/커서

- [`src/model.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/model.coffee)
- [`src/helper.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/helper.coffee)
- [`src/treewalk.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/treewalk.coffee)

### 뷰/렌더링

- [`src/view.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/view.coffee)
- [`src/draw.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/draw.coffee)
- [`css/droplet.css`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/css/droplet.css)

### 언어 parser

- [`src/parser.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/parser.coffee)
- [`src/modes.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/modes.coffee)
- [`src/languages/javascript.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/languages/javascript.coffee)
- [`src/languages/python.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/languages/python.coffee)
- [`src/languages/coffee.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/languages/coffee.coffee)

### 테스트/설계

- [`DESIGN.md`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/DESIGN.md)
- [`EMBEDDING.md`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/EMBEDDING.md)
- [`test/src/modelTests.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/test/src/modelTests.coffee)
- [`test/src/parserTests.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/test/src/parserTests.coffee)
- [`test/src/uitest.coffee`](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/test/src/uitest.coffee)

## UI 이벤트와 데이터 흐름

원문 text → parser·AST source bounds → block/socket/indent marker → linked list model → canvas view. 드래그 편집 시 해당 token range를 수정하여 text를 보존한다.

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

Droplet은 일반적인 Blockly를 내부 블록 AST로 쓰지 않으므로 타사 Blockly 플러그인과 동일한 구조로 취급하면 안 됨.

### 다음 단계의 검증 작업

- 각 핵심 파일의 실제 함수·클래스와 호출 위치를 줄 번호 단위로 추출
- 화면별 버튼 event handler ↔ 내부 action 매핑
- 코드 전환 전후 스냅샷 및 회귀 테스트 실행
- 로그인·공유·저장·미리보기의 request/response 경로 분리
