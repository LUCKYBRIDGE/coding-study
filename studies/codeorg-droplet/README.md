# Code.org App Lab / Game Lab — 서비스별 구조·화면·기술 심층 기록

> 기준: 2026-11-08 · 근거: 공개 코드 트리는 GitHub API의 일부가 truncated; 확인한 파일 경로 위주  
> 원본: [code-dot-org/code-dot-org](https://github.com/code-dot-org/code-dot-org) · [검토 커밋](https://github.com/code-dot-org/code-dot-org/tree/a37e33c1452c419bd72156f551d76d1f74028b18)  
> 주의: 아래 ‘화면’은 서비스에서 확인 가능한 주요 화면/모드의 **기능 단위 목록**입니다. 정확한 현행 URL, 화면 배치, 버튼 라벨을 모두 직접 클릭 검증했다는 뜻은 아닙니다.

## 1. 실제 프로젝트 파일·폴더

- `apps/src/applab/DesignWorkspace.jsx` — UI 설계
- `apps/src/applab/DesignToolbox.jsx` — UI 요소 도구
- `apps/src/applab/AppLabView.jsx` — 앱 구성 뷰
- `apps/src/applab/ScreenSelector.jsx` — 화면 선택
- `apps/src/applab/EventSandboxer.js` — 이벤트 격리
- `apps/src/applab/Exporter.js` — 결과물 추출
- `apps/src/p5lab/gamelab/GameLab.js` — 게임 로직
- `apps/src/p5lab/gamelab/dropletConfig.js` — block mapping
- `apps/src/applab/dropletConfig.js` — 앱 블록 설정
- `apps/src/dropletUtils.js` — editor 설정 유틸

**검증 가능한 원본 링크:** [apps/src/applab/DesignWorkspace.jsx](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/DesignWorkspace.jsx) · [apps/src/applab/DesignToolbox.jsx](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/DesignToolbox.jsx) · [apps/src/applab/AppLabView.jsx](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/AppLabView.jsx) · [apps/src/applab/ScreenSelector.jsx](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/ScreenSelector.jsx) · [apps/src/applab/EventSandboxer.js](https://github.com/code-dot-org/code-dot-org/blob/a37e33c1452c419bd72156f551d76d1f74028b18/apps/src/applab/EventSandboxer.js)

## 2. 화면/페이지 단위 인벤토리

1. **App Lab — Design Mode/Code Mode/Preview**
2. **Game Lab — 게임 제작·실행 화면**
3. **JavaScript Droplet block/text 편집 영역**
4. **수업 수준·과제별 제한·설명**
5. **작품 가져오기·내보내기·공유**

**구분:** 로그인 여부·기기·target·학습 코스에 따라 화면이 달라질 수 있습니다. 각 URL과 팝업/모바일 화면은 실기기·브라우저 재현 검증이 필요한 항목입니다.

## 3. 버튼·행동 → 기술적 결과

| UI 행동 또는 기능 | 동작 및 연결 구조 | 증거 |
|---|---|---|
| Design Mode (App Lab) | 화면 요소 배치, DesignWorkspace/Toolbox 로직 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Code Mode | Droplet 기반 JavaScript block/text | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Run/Reset | App Lab/Game Lab 실행 엔진과 미리보기 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| UI event handlers | 디자인 요소 id와 JS 이벤트 연결 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Import / Export | Importer/Exporter 컴포넌트로 별도 흐름 관리 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 선생님 과제/제한 | 코드 편집기 설정이 문제별로 다를 수 있음 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |

버튼을 누른 뒤 **어떤 상태를 변경하고, 어떤 변환 작업·저장 작업이 발생하며, 언제 오류 또는 확인 대화상자가 나타나는지**를 별개의 검증 대상으로 취급합니다. 소스에 존재하는 기능과 현재 공개 UI에 노출되는 기능도 같다고 가정하지 않습니다.

## 4. 얽힌 문제와 실제 해결 접근

| 충돌 지점 | 구현 또는 설계에서 확인한 해결 접근 |
|---|---|
| 사용자 앱 코드와 서비스 런타임 분리 | sandbox/event 조정 계층 활용 |
| 다양한 교육 API를 블록화 | 제품별 dropletConfig.js |
| 디자인 요소와 JS 코드 연결 | 화면 요소 ID/이벤트 API 설계 |
| 큰 저장소의 변경 영향 | App Lab, Game Lab, 공통 code-studio 계층을 나눠 추적 |

## 5. UI/UX 평가 프레임

- **발견성:** 전환 모드/기본 실행 버튼을 초보 학습자가 즉시 찾는가?
- **맥락 보존:** 전환 후 동일 문맥(선택 블록, 코드 줄, 스크롤 위치)이 유지되는가?
- **오류 복구:** 잘못 입력한 코드가 사라지는가, 보존되는가, 다시 편집할 수 있는가?
- **학습 지원:** 블록과 텍스트의 1:1 대응 또는 추상화 차이를 설명하는가?
- **작업 안정성:** 자동 저장·Undo·사본 생성·실행 중 편집·기기 연결이 충돌하지 않는가?
- **접근성:** 터치/키보드/화면 리더/작은 화면/한글 폰트가 대응되는가?

현재 수치 점수는 매기지 않았습니다. 이 항목들은 **브라우저 조작과 사용성 관찰**로 채워야 합니다.

## 6. 우리 설계에 참고할 핵심

문법 변환 이외에 UI 빌더·교육 도구·실행 샌드박스가 결합되는 대규모 제품 사례. Code.org 모든 앱을 동일 편집기로 일반화하면 안 된다.

## 7. 추가 검증 백로그

1. 화면별 실제 URL/메뉴 이동·반응형 차이 캡처
2. 모든 주요 버튼 클릭 전후 상태와 예외 조건 기록
3. 작은/큰 코드에 대한 변환 지연 및 Undo/Redo
4. 주석·오류·미지원 문법의 양방향 보존성 실험
5. 저장 포맷·프라이버시·라이선스 확인
6. 코드 근거에서 확인된 내부 상태 관리와 UI 이벤트를 연결한 sequence diagram

## 8. 분석 신뢰성

- **확인:** 위에 열거한 파일·폴더의 존재와 일부 공개 코드/설계 원리
- **설명/기능 수준:** 공개 문서와 기존 조사에 근거한 주요 화면·기능
- **미확인:** 모든 화면의 현재 버튼 좌표·버튼 명칭·클릭 결과·모바일 동작을 실제로 한 번씩 시험한 것은 아님
- **금지:** 공개 소스에 없는 백엔드 테이블명·내부 API·알고리즘을 추정해 실제 구조처럼 작성

관련 기존 상세 분석: [기존 Code.org App Lab / Game Lab 기술 분석](../../docs/platforms/codeorg-droplet.md)
