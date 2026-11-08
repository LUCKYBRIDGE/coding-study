# BlockPy / BlockMirror — 서비스별 구조·화면·기술 심층 기록

> 기준: 2026-11-08 · 근거: BlockMirror 실제 코드 및 공개 트리 확인; BlockPy 상위 서비스와 분리  
> 원본: [blockpy-edu/BlockMirror](https://github.com/blockpy-edu/BlockMirror) · [검토 커밋](https://github.com/blockpy-edu/BlockMirror/tree/9b77174b297c60bed07992d40fc9a00885291e1c)  
> 주의: 아래 ‘화면’은 서비스에서 확인 가능한 주요 화면/모드의 **기능 단위 목록**입니다. 정확한 현행 URL, 화면 배치, 버튼 라벨을 모두 직접 클릭 검증했다는 뜻은 아닙니다.

## 1. 실제 프로젝트 파일·폴더

- `src/block_mirror.js` — 공유 코드·모드·이벤트
- `src/block_editor.js` — Blockly workspace·generator
- `src/text_editor.js` — CodeMirror·키보드/편집 이벤트
- `src/text_to_blocks.js` — Skulpt Python AST→XML
- `src/ast/ast_*.js (46개)` — 문법 노드별 양방향 매핑
- `src/skulpt/parser.js, ast.js` — Python parser
- `src/toolbars.js` — toolbox 구성
- `webpack.config.js` — 배포 번들
- `test/simple*.html` — 샘플 구성

**검증 가능한 원본 링크:** [src/block_mirror.js](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/block_mirror.js) · [src/block_editor.js](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/block_editor.js) · [src/text_editor.js](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/text_editor.js) · [src/text_to_blocks.js](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/text_to_blocks.js) · [src/ast/ast_*.js (46개)](https://github.com/blockpy-edu/BlockMirror/blob/9b77174b297c60bed07992d40fc9a00885291e1c/src/ast/ast_*.js (46개))

## 2. 화면/페이지 단위 인벤토리

1. **Block/Texts/Split 세 가지 표시 모드**
2. **Blockly toolbox·작업공간**
3. **CodeMirror Python 편집기**
4. **실행·과제 환경은 상위 BlockPy에 의존**
5. **test/simple.html 등 라이브러리 실험 페이지**

**구분:** 로그인 여부·기기·target·학습 코스에 따라 화면이 달라질 수 있습니다. 각 URL과 팝업/모바일 화면은 실기기·브라우저 재현 검증이 필요한 항목입니다.

## 3. 버튼·행동 → 기술적 결과

| UI 행동 또는 기능 | 동작 및 연결 구조 | 증거 |
|---|---|---|
| setMode('split'|'block'|'text') | 양쪽 편집기 표시 변경 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| setCode/getCode | Python source 공유 모델을 갱신/조회 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 블록 추가·수정 | Blockly Python generator→공유 source→텍스트 갱신 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Python 직접 수정 | Skulpt parse→Python AST→Blockly XML→workspace 갱신 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 코드 오류 입력 | raw code block fallback으로 원문을 보존하는 분기 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| run (configuration) | 실행 콜백을 호스트 앱에서 제공 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |

버튼을 누른 뒤 **어떤 상태를 변경하고, 어떤 변환 작업·저장 작업이 발생하며, 언제 오류 또는 확인 대화상자가 나타나는지**를 별개의 검증 대상으로 취급합니다. 소스에 존재하는 기능과 현재 공개 UI에 노출되는 기능도 같다고 가정하지 않습니다.

## 4. 얽힌 문제와 실제 해결 접근

| 충돌 지점 | 구현 또는 설계에서 확인한 해결 접근 |
|---|---|
| 양쪽 editor 무한 업데이트 | silenceBlock/silenceText/silenceModel 등 상호 이벤트 억제 |
| Python 일부가 구문 오류 | 가능한 앞부분 parse 후 오류 chunk를 raw block으로 남기는 fallback |
| 코드가 길어 파싱이 느림 | blockDelay와 지연/병합 갱신 |
| 좁은 화면 split view | 넓은 화면 가로 분할, 좁으면 상하 배치 |
| 주석·소스 순서 | comment 위치 map과 source line 정보 이용 |

## 5. UI/UX 평가 프레임

- **발견성:** 전환 모드/기본 실행 버튼을 초보 학습자가 즉시 찾는가?
- **맥락 보존:** 전환 후 동일 문맥(선택 블록, 코드 줄, 스크롤 위치)이 유지되는가?
- **오류 복구:** 잘못 입력한 코드가 사라지는가, 보존되는가, 다시 편집할 수 있는가?
- **학습 지원:** 블록과 텍스트의 1:1 대응 또는 추상화 차이를 설명하는가?
- **작업 안정성:** 자동 저장·Undo·사본 생성·실행 중 편집·기기 연결이 충돌하지 않는가?
- **접근성:** 터치/키보드/화면 리더/작은 화면/한글 폰트가 대응되는가?

현재 수치 점수는 매기지 않았습니다. 이 항목들은 **브라우저 조작과 사용성 관찰**로 채워야 합니다.

## 6. 우리 설계에 참고할 핵심

Python 중심 블록↔텍스트 구현에 가장 직접적으로 참고할 파일 단위 구조. BlockMirror 코드와 BlockPy 학습 플랫폼 기능을 혼동하지 않는다.

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

관련 기존 상세 분석: [기존 BlockPy / BlockMirror 기술 분석](../../docs/platforms/blockpy-blockmirror.md)
