# Droplet Editor — 서비스별 구조·화면·기술 심층 기록

> 기준: 2026-11-08 · 근거: README/DESIGN.md 및 실제 src 트리 확인  
> 원본: [droplet-editor/droplet](https://github.com/droplet-editor/droplet) · [검토 커밋](https://github.com/droplet-editor/droplet/tree/bc724da8880b278388051e5f0f7f2ddd36e005fc)  
> 주의: 아래 ‘화면’은 서비스에서 확인 가능한 주요 화면/모드의 **기능 단위 목록**입니다. 정확한 현행 URL, 화면 배치, 버튼 라벨을 모두 직접 클릭 검증했다는 뜻은 아닙니다.

## 1. 실제 프로젝트 파일·폴더

- `src/model.coffee` — linked-list tokens와 mutation
- `src/view.coffee` — block 도형 렌더링
- `src/controller.coffee` — 이벤트·Undo·Ace 연동
- `src/parser.coffee` — source range→마크업
- `src/treewalk.coffee` — AST 탐색
- `src/languages/*.coffee` — JS/Coffee/Python/HTML 등 parser adapter
- `src/modes.coffee` — 언어 모드 등록
- `example/` — 임베드 실험 페이지
- `test/src/` — parser/model/UI 회귀 테스트
- `DESIGN.md` — 공식 설계 원리

**검증 가능한 원본 링크:** [src/model.coffee](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/model.coffee) · [src/view.coffee](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/view.coffee) · [src/controller.coffee](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/controller.coffee) · [src/parser.coffee](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/parser.coffee) · [src/treewalk.coffee](https://github.com/droplet-editor/droplet/blob/bc724da8880b278388051e5f0f7f2ddd36e005fc/src/treewalk.coffee)

## 2. 화면/페이지 단위 인벤토리

1. **통합 편집기(임베드 컴포넌트)**
2. **블록 모드: 소스에 구조적 블록 테두리/소켓**
3. **텍스트 모드: Ace 기반 텍스트 편집**
4. **example/example.html 등 언어별 예제 페이지**
5. **테스트 UI: test/test.html, uitest.html**

**구분:** 로그인 여부·기기·target·학습 코스에 따라 화면이 달라질 수 있습니다. 각 URL과 팝업/모바일 화면은 실기기·브라우저 재현 검증이 필요한 항목입니다.

## 3. 버튼·행동 → 기술적 결과

| UI 행동 또는 기능 | 동작 및 연결 구조 | 증거 |
|---|---|---|
| setEditorState | block/text mode 전환 API | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| setValue/getValue | 문서 텍스트 값 입력/획득 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 팔레트에서 블록 추가 | 문자열/구조 마크업을 편집하는 모델에 반영 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Drag & Drop | block/socket/indent의 위치·precedence 규칙에 맞춰 삽입 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| Undo/Redo | model의 insert/remove/replace에서 되돌리기 연산 반환 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |

버튼을 누른 뒤 **어떤 상태를 변경하고, 어떤 변환 작업·저장 작업이 발생하며, 언제 오류 또는 확인 대화상자가 나타나는지**를 별개의 검증 대상으로 취급합니다. 소스에 존재하는 기능과 현재 공개 UI에 노출되는 기능도 같다고 가정하지 않습니다.

## 4. 얽힌 문제와 실제 해결 접근

| 충돌 지점 | 구현 또는 설계에서 확인한 해결 접근 |
|---|---|
| 코드를 다시 생성하면 원문 포맷이 바뀜 | 원문 텍스트를 linked-list text token으로 보존하고 구조 마크업을 덧씌움 |
| 블록 이동 시 공백·들여쓰기 변경 | token splice/replace와 whitespace 조정 |
| 커서 위치 불일치 | source location과 mutation tracking |
| 언어별 구문 차이 | 언어별 parser adapter·block bounds 구현 |

## 5. UI/UX 평가 프레임

- **발견성:** 전환 모드/기본 실행 버튼을 초보 학습자가 즉시 찾는가?
- **맥락 보존:** 전환 후 동일 문맥(선택 블록, 코드 줄, 스크롤 위치)이 유지되는가?
- **오류 복구:** 잘못 입력한 코드가 사라지는가, 보존되는가, 다시 편집할 수 있는가?
- **학습 지원:** 블록과 텍스트의 1:1 대응 또는 추상화 차이를 설명하는가?
- **작업 안정성:** 자동 저장·Undo·사본 생성·실행 중 편집·기기 연결이 충돌하지 않는가?
- **접근성:** 터치/키보드/화면 리더/작은 화면/한글 폰트가 대응되는가?

현재 수치 점수는 매기지 않았습니다. 이 항목들은 **브라우저 조작과 사용성 관찰**로 채워야 합니다.

## 6. 우리 설계에 참고할 핵심

텍스트를 정본으로 유지하며 그 위에 구조적 조작 인터페이스를 씌우는 대안. UI 구현 복잡도가 매우 높다.

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

관련 기존 상세 분석: [기존 Droplet Editor 기술 분석](../../docs/platforms/codeorg-droplet.md)
