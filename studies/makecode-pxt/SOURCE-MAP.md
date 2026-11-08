# 소스코드 파일별 연결 지도 — microsoft/pxt

> 근거: [확인한 Git 트리](https://github.com/microsoft/pxt/tree/5679f3c458a204467be71d32f609b515d2972cd2) · commit `5679f3c458a204467be71d32f609b515d2972cd2`  
> 주의: 저장소 실제 파일명과 조사 목적 설명을 연결한 **소스 탐색 지도**이며 모든 함수 호출 관계를 정적 분석·실행 검증한 결과는 아님.

## 대표 모듈/페이지 구성

### 화면/전환

- [`webapp/src/app.tsx`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/app.tsx)
- [`webapp/src/blocks.tsx`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/blocks.tsx)
- [`webapp/src/monaco.tsx`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/monaco.tsx)

### 블록 컴파일

- [`pxtblocks/compiler/compiler.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtblocks/compiler/compiler.ts)
- [`pxtblocks/compiler/typeChecker.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtblocks/compiler/typeChecker.ts)
- [`pxtblocks/compiler/environment.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtblocks/compiler/environment.ts)

### 텍스트/디컴파일

- [`pxtcompiler/emitter/decompiler.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtcompiler/emitter/decompiler.ts)
- [`pxtcompiler/emitter/transpile.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtcompiler/emitter/transpile.ts)
- [`pxtcompiler/emitter/service.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtcompiler/emitter/service.ts)

### Python

- [`pxtpy/parser.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtpy/parser.ts)
- [`pxtpy/converter.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtpy/converter.ts)
- [`pxtpy/pydecompiler.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtpy/pydecompiler.ts)

### 실행/디버깅

- [`pxtsim/runtime.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtsim/runtime.ts)
- [`pxtsim/debugger.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtsim/debugger.ts)
- [`webapp/src/compiler.ts`](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/compiler.ts)

## UI 이벤트와 데이터 흐름

Blocks event → workspace state → Blockly compiler → TypeScript → simulator/hardware. Python edit → Python parser/py2ts → TypeScript; Blocks 복귀 → decompileToBlocks. UI 전환은 app.tsx 및 각 editor component 간 orchestration.

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

이 파일 목록은 메인 PXT 엔진이며 arcade·micro:bit 등 target별 프런트엔드/툴박스는 별도 저장소에 있을 수 있음.

### 다음 단계의 검증 작업

- 각 핵심 파일의 실제 함수·클래스와 호출 위치를 줄 번호 단위로 추출
- 화면별 버튼 event handler ↔ 내부 action 매핑
- 코드 전환 전후 스냅샷 및 회귀 테스트 실행
- 로그인·공유·저장·미리보기의 request/response 경로 분리
