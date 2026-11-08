# 소스코드 파일별 연결 지도 — PencilCode/pencilcode

> 근거: [확인한 Git 트리](https://github.com/PencilCode/pencilcode/tree/40947d9a578fe5b926979198f1957f6ec6df486d) · commit `40947d9a578fe5b926979198f1957f6ec6df486d`  
> 주의: 저장소 실제 파일명과 조사 목적 설명을 연결한 **소스 탐색 지도**이며 모든 함수 호출 관계를 정적 분석·실행 검증한 결과는 아님.

## 대표 모듈/페이지 구성

### 페이지 템플릿

- [`content/welcome.html`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/welcome.html)
- [`content/dir.html`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/dir.html)
- [`content/editor.html`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/editor.html)
- [`content/framed.html`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/framed.html)

### 에디터

- [`content/src/editor-main.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/editor-main.js)
- [`content/src/controller.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/controller.js)
- [`content/src/view.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/view.js)
- [`content/src/palette.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/palette.js)

### 실행/안내

- [`content/src/debug.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/debug.js)
- [`content/src/guide.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/guide.js)
- [`content/src/showturtle.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/showturtle.js)

### 파일/저장

- [`content/src/storage.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/storage.js)
- [`content/src/filetype.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/filetype.js)
- [`server/load.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/server/load.js)
- [`server/save.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/server/save.js)
- [`server/filemeta.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/server/filemeta.js)

### 서버

- [`server/server.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/server/server.js)
- [`server/serverbase.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/server/serverbase.js)
- [`server/socket.js`](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/server/socket.js)

## UI 이벤트와 데이터 흐름

welcome/dir → editor controller/view → Droplet dual mode → local storage/server load·save → 실행/미리보기. editor mode 설정과 파일 source의 저장은 독립된 관심사다.

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

현재 Pencil Code의 운영 UI와 이 공개 저장소 파일이 완전히 같은 배포 버전인지 별도 검증이 필요.

### 다음 단계의 검증 작업

- 각 핵심 파일의 실제 함수·클래스와 호출 위치를 줄 번호 단위로 추출
- 화면별 버튼 event handler ↔ 내부 action 매핑
- 코드 전환 전후 스냅샷 및 회귀 테스트 실행
- 로그인·공유·저장·미리보기의 request/response 경로 분리
