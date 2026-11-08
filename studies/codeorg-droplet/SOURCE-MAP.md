# 소스코드 파일별 연결 지도 — code-dot-org/code-dot-org

> 근거: [확인한 Git 트리](https://github.com/code-dot-org/code-dot-org/tree/a37e33c1452c419bd72156f551d76d1f74028b18) · commit `a37e33c1452c419bd72156f551d76d1f74028b18`  
> 주의: 저장소 실제 파일명과 조사 목적 설명을 연결한 **소스 탐색 지도**이며 모든 함수 호출 관계를 정적 분석·실행 검증한 결과는 아님.

## 대표 모듈/페이지 구성

### App Lab 페이지 구성

- [`apps/src/applab/AppLabView.jsx`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/AppLabView.jsx)
- [`apps/src/applab/DesignWorkspace.jsx`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/DesignWorkspace.jsx)
- [`apps/src/applab/DesignToolbox.jsx`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/DesignToolbox.jsx)
- [`apps/src/applab/ScreenSelector.jsx`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/ScreenSelector.jsx)

### Game Lab

- [`apps/src/p5lab/gamelab/GameLab.js`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/p5lab/gamelab/GameLab.js)
- [`apps/src/p5lab/gamelab/dropletConfig.js`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/p5lab/gamelab/dropletConfig.js)

### Droplet 설정

- [`apps/src/applab/dropletConfig.js`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/dropletConfig.js)
- [`apps/src/dropletUtils.js`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/dropletUtils.js)

### 사용자 앱 실행

- [`apps/src/applab/EventSandboxer.js`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/EventSandboxer.js)
- [`apps/src/applab/api.js`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/api.js)

### 입출력

- [`apps/src/applab/Exporter.js`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/Exporter.js)
- [`apps/src/applab/ImportProjectDialog.jsx`](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/ImportProjectDialog.jsx)

## UI 이벤트와 데이터 흐름

App Lab: DesignWorkspace UI 요소 변경 + JavaScript Droplet 편집 → 이벤트 API로 연결 → 앱 Preview. Game Lab: 별도 game runtime/API와 Droplet mapping. 공통 사이트 저장/과제 계층은 apps만으로 설명되지 않음.

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

monorepo Git tree 응답이 truncated이며 파일 목록은 직접 확인된 부분에만 한정. 특정 코드 파일이 현재 최종 배포 라우트에서 실제 사용되는지는 추가 추적 대상.

### 다음 단계의 검증 작업

- 각 핵심 파일의 실제 함수·클래스와 호출 위치를 줄 번호 단위로 추출
- 화면별 버튼 event handler ↔ 내부 action 매핑
- 코드 전환 전후 스냅샷 및 회귀 테스트 실행
- 로그인·공유·저장·미리보기의 request/response 경로 분리
