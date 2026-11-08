# Pencil Code — 서비스별 구조·화면·기술 심층 기록

> 기준: 2026-11-08 · 근거: 공개 서비스 저장소 실제 트리와 Droplet 의존성 확인  
> 원본: [PencilCode/pencilcode](https://github.com/PencilCode/pencilcode) · [검토 커밋](https://github.com/PencilCode/pencilcode/tree/40947d9a578fe5b926979198f1957f6ec6df486d)  
> 주의: 아래 ‘화면’은 서비스에서 확인 가능한 주요 화면/모드의 **기능 단위 목록**입니다. 정확한 현행 URL, 화면 배치, 버튼 라벨을 모두 직접 클릭 검증했다는 뜻은 아닙니다.

## 1. 실제 프로젝트 파일·폴더

- `content/welcome.html, dir.html` — 진입·목록
- `content/editor.html, framed.html` — 에디터
- `content/src/controller.js` — 편집기 조정
- `content/src/view.js` — Droplet UI 연결
- `content/src/storage.js` — 브라우저 저장·복구
- `content/src/debug.js` — 실행/오류
- `content/src/palette.js` — 블록 팔레트
- `server/load.js, save.js` — 서버 파일 API
- `server/filemeta.js` — 파일 메타데이터
- `server/socket.js` — 서버 실시간 연결 관련 코드

**검증 가능한 원본 링크:** [content/welcome.html, dir.html](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/welcome.html, dir.html) · [content/editor.html, framed.html](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/editor.html, framed.html) · [content/src/controller.js](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/controller.js) · [content/src/view.js](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/view.js) · [content/src/storage.js](https://github.com/PencilCode/pencilcode/blob/40947d9a578fe5b926979198f1957f6ec6df486d/content/src/storage.js)

## 2. 화면/페이지 단위 인벤토리

1. **content/welcome.html — 진입/작품 시작**
2. **content/dir.html — 파일·폴더 보기**
3. **content/editor.html — 주 편집 화면**
4. **content/framed.html — embedded 편집**
5. **사용자별 공유·파일 경로 — 서버 load/save 흐름**

**구분:** 로그인 여부·기기·target·학습 코스에 따라 화면이 달라질 수 있습니다. 각 URL과 팝업/모바일 화면은 실기기·브라우저 재현 검증이 필요한 항목입니다.

## 3. 버튼·행동 → 기술적 결과

| UI 행동 또는 기능 | 동작 및 연결 구조 | 증거 |
|---|---|---|
| Blocks/Text toggle | Droplet에 의한 양쪽 편집 방식 변경 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Run | 작성 문서를 실행/미리보기하는 화면 로직 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Save | 파일 메타데이터와 source 저장 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 폴더·파일 선택 | 문서/폴더 뷰 및 저장 경로 연결 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 디버깅 | content/src/debug.js 중심 UI | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 공유 주소 | 공개된 파일 URL 기반 접근, 개인정보 주의 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |

버튼을 누른 뒤 **어떤 상태를 변경하고, 어떤 변환 작업·저장 작업이 발생하며, 언제 오류 또는 확인 대화상자가 나타나는지**를 별개의 검증 대상으로 취급합니다. 소스에 존재하는 기능과 현재 공개 UI에 노출되는 기능도 같다고 가정하지 않습니다.

## 4. 얽힌 문제와 실제 해결 접근

| 충돌 지점 | 구현 또는 설계에서 확인한 해결 접근 |
|---|---|
| 블록과 코드 표현이 멀다 | Droplet으로 실제 source syntax를 block처럼 조작 |
| 파일을 여러 곳에서 다룸 | 브라우저 저장·서버 파일 로딩 계층 분리 |
| 텍스트 전환 후 소스 보존 | Droplet 원문 보존 모델에 의존 |
| 교육용 공개 공유 위험 | public 저장물/계정 정책을 수업용 개인정보 모델과 분리해야 함 |

## 5. UI/UX 평가 프레임

- **발견성:** 전환 모드/기본 실행 버튼을 초보 학습자가 즉시 찾는가?
- **맥락 보존:** 전환 후 동일 문맥(선택 블록, 코드 줄, 스크롤 위치)이 유지되는가?
- **오류 복구:** 잘못 입력한 코드가 사라지는가, 보존되는가, 다시 편집할 수 있는가?
- **학습 지원:** 블록과 텍스트의 1:1 대응 또는 추상화 차이를 설명하는가?
- **작업 안정성:** 자동 저장·Undo·사본 생성·실행 중 편집·기기 연결이 충돌하지 않는가?
- **접근성:** 터치/키보드/화면 리더/작은 화면/한글 폰트가 대응되는가?

현재 수치 점수는 매기지 않았습니다. 이 항목들은 **브라우저 조작과 사용성 관찰**로 채워야 합니다.

## 6. 우리 설계에 참고할 핵심

기술적 에디터 자체보다 ‘기존 엔진을 교육용 서비스 전체와 결합’한 사례. 실제 운영 정책도 따로 검토해야 한다.

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

관련 기존 상세 분석: [기존 Pencil Code 기술 분석](../../docs/platforms/pencil-code.md)
