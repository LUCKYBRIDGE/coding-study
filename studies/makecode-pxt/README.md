# Microsoft MakeCode / PXT — 서비스별 구조·화면·기술 심층 기록

> 기준: 2026-11-08 · 근거: 공개 저장소 트리·파일 직접 확인  
> 원본: [microsoft/pxt](https://github.com/microsoft/pxt) · [검토 커밋](https://github.com/microsoft/pxt/tree/5679f3c458a204467be71d32f609b515d2972cd2)  
> 주의: 아래 ‘화면’은 서비스에서 확인 가능한 주요 화면/모드의 **기능 단위 목록**입니다. 정확한 현행 URL, 화면 배치, 버튼 라벨을 모두 직접 클릭 검증했다는 뜻은 아닙니다.

## 1. 실제 프로젝트 파일·폴더

- `webapp/src/app.tsx` — 전체 에디터/화면 전환 관리
- `webapp/src/blocks.tsx` — Blockly 화면·작업공간
- `webapp/src/monaco.tsx` — 텍스트 편집기 및 오류 목록
- `webapp/src/compiler.ts` — 컴파일 작업 orchestration
- `pxtblocks/compiler/compiler.ts` — Blocks→TS compiler
- `pxtcompiler/emitter/decompiler.ts` — TS→Blocks
- `pxtcompiler/emitter/transpile.ts` — Python↔TS
- `pxtpy/parser.ts, converter.ts, pydecompiler.ts` — Python 처리
- `pxtsim/runtime.ts, debugger.ts` — 시뮬레이터/디버깅
- `pxtlib/main.ts` — main.blocks/main.ts/main.py 상수

**검증 가능한 원본 링크:** [webapp/src/app.tsx](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/app.tsx) · [webapp/src/blocks.tsx](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/blocks.tsx) · [webapp/src/monaco.tsx](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/monaco.tsx) · [webapp/src/compiler.ts](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/webapp/src/compiler.ts) · [pxtblocks/compiler/compiler.ts](https://github.com/microsoft/pxt/blob/5679f3c458a204467be71d32f609b515d2972cd2/pxtblocks/compiler/compiler.ts)

## 2. 화면/페이지 단위 인벤토리

1. **프로젝트 선택/홈(제품별 UI는 target 의존)**
2. **Blocks 작업 화면: workspace, toolbox, simulator**
3. **JavaScript/TypeScript 편집 화면**
4. **Python 편집 화면(지원 target 한정)**
5. **확장 기능·교육용 튜토리얼·공유 흐름(제품별 차이)**

**구분:** 로그인 여부·기기·target·학습 코스에 따라 화면이 달라질 수 있습니다. 각 URL과 팝업/모바일 화면은 실기기·브라우저 재현 검증이 필요한 항목입니다.

## 3. 버튼·행동 → 기술적 결과

| UI 행동 또는 기능 | 동작 및 연결 구조 | 증거 |
|---|---|---|
| Blocks ↔ JavaScript | 언어 모드 전환 시 코드 생성·디컴파일, 지원 문법 한정 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Python | Python 지원 target에서만 등장하며 py2ts/tsToPy 계층 경유 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Run / simulator | 컴파일 결과와 simulator runtime 연결 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Extensions | API와 block annotation을 확장하며 decompiler 호환성에 영향 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Download / Share | target, 편집기 상태와 호스팅에 따라 사용성 달라짐 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |

버튼을 누른 뒤 **어떤 상태를 변경하고, 어떤 변환 작업·저장 작업이 발생하며, 언제 오류 또는 확인 대화상자가 나타나는지**를 별개의 검증 대상으로 취급합니다. 소스에 존재하는 기능과 현재 공개 UI에 노출되는 기능도 같다고 가정하지 않습니다.

## 4. 얽힌 문제와 실제 해결 접근

| 충돌 지점 | 구현 또는 설계에서 확인한 해결 접근 |
|---|---|
| 지원하지 않는 TypeScript 문법 | API metadata에 따라 블록으로 되돌릴 수 있는 부분만 디컴파일; grey block 관련 탈출구·diagnostic 구분 |
| 변환 후 블록 ID 소실 | 코드상 Blocks→TS roundtrip 후 ID가 바뀔 수 있어 semantic equality와 layout equality를 별개로 봐야 함 |
| 언어가 3개 이상 | Python→TS, Blocks→TS, TS→Python/Blocks처럼 공통 축으로 연결 |
| 확장 API | annotation 기반으로 block mapping을 선언하여 단순 문자열 생성과 다르게 처리 |

## 5. UI/UX 평가 프레임

- **발견성:** 전환 모드/기본 실행 버튼을 초보 학습자가 즉시 찾는가?
- **맥락 보존:** 전환 후 동일 문맥(선택 블록, 코드 줄, 스크롤 위치)이 유지되는가?
- **오류 복구:** 잘못 입력한 코드가 사라지는가, 보존되는가, 다시 편집할 수 있는가?
- **학습 지원:** 블록과 텍스트의 1:1 대응 또는 추상화 차이를 설명하는가?
- **작업 안정성:** 자동 저장·Undo·사본 생성·실행 중 편집·기기 연결이 충돌하지 않는가?
- **접근성:** 터치/키보드/화면 리더/작은 화면/한글 폰트가 대응되는가?

현재 수치 점수는 매기지 않았습니다. 이 항목들은 **브라우저 조작과 사용성 관찰**로 채워야 합니다.

## 6. 우리 설계에 참고할 핵심

중간 의미 계층과 표현별 파일을 분리한다. 단, 사용자 임의 JS/Python 전체의 완전한 왕복이라고 표현하지 않는다.

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

관련 기존 상세 분석: [기존 Microsoft MakeCode / PXT 기술 분석](../../docs/platforms/makecode-pxt.md)
