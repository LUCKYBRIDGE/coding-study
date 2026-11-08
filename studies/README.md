# 서비스별 심층 분석 인덱스

> 기준일 2026-11-08 · 각 폴더의 README는 UI/UX·화면·기능·복잡한 문제의 해결법을, SOURCE-MAP은 공개 저장소의 실제 파일 경로를 기록합니다.  
> 기존 `docs/platforms/*.md`는 초기 기능·아키텍처 조사이며, 이 디렉터리는 서비스별 증거 중심의 구조 분석입니다.

## 현재 범위

- 18개 서비스·엔진별 독립 폴더
- 공개 소스를 확인한 10개 폴더에 별도 `SOURCE-MAP.md`
- 비공개/핵심 비공개 제품은 공개 기능과 미확인 내부 구현을 분리
- 공통 주장 상태와 evidence record 형식은 [증거 원장](../docs/05-evidence-ledger.md)에 통일

### 양방향 구현과 에디터 엔진

- [makecode-pxt](./makecode-pxt/README.md) · [실제 소스 지도](./makecode-pxt/SOURCE-MAP.md)
- [droplet](./droplet/README.md) · [실제 소스 지도](./droplet/SOURCE-MAP.md)
- [blockpy-blockmirror](./blockpy-blockmirror/README.md) · [실제 소스 지도](./blockpy-blockmirror/SOURCE-MAP.md)
- [pencil-code](./pencil-code/README.md) · [실제 소스 지도](./pencil-code/SOURCE-MAP.md)
- [codeorg-droplet](./codeorg-droplet/README.md) · [실제 소스 지도](./codeorg-droplet/SOURCE-MAP.md)

### 블록 네이티브·기반 엔진

- [blockly](./blockly/README.md) · [실제 소스 지도](./blockly/SOURCE-MAP.md)
- [mit-app-inventor](./mit-app-inventor/README.md) · [실제 소스 지도](./mit-app-inventor/SOURCE-MAP.md)
- [snap](./snap/README.md) · [실제 소스 지도](./snap/SOURCE-MAP.md)
- [scratch-editor](./scratch-editor/README.md) · [실제 소스 지도](./scratch-editor/SOURCE-MAP.md)

이 네 항목은 ‘텍스트 전환 기능이 많아서’가 아니라 **블록 editor·내부 IR/AST·runtime·persistence의 책임 경계**를 비교하기 위한 기준군입니다.

### 다중 하드웨어·코드 생성

- [open-roberta](./open-roberta/README.md) · [실제 소스 지도](./open-roberta/SOURCE-MAP.md)
- [mindplus](./mindplus/README.md)
- [mblock5](./mblock5/README.md)

### 단방향·수준별 전환

- [edublocks](./edublocks/README.md)
- [pictoblox](./pictoblox/README.md)
- [tinkercad-circuits](./tinkercad-circuits/README.md)
- [lego-spike](./lego-spike/README.md)

### 부분 텍스트/연구

- [vexcode-switch](./vexcode-switch/README.md)
- [blockpython-2026](./blockpython-2026/README.md)

## 모든 서비스에서 같은 기준으로 추적할 것

| 분석 축 | 관찰 대상 | 확인 가능한 증거 |
|---|---|---|
| 화면 구조 | 홈·프로젝트·편집·실행·설정·공유·학급 | URL/스크린샷/라우트/페이지 파일 |
| UI 요소 | 메뉴·버튼·툴팁·모달·단축키·반응형 | 실제 이벤트/DOM/공식 화면 |
| 기능 흐름 | 사용자 액션 → 데이터 변화 → 피드백 | handler/call chain/재현 절차 |
| 모델·상태 | 정본, 변경 이력, 두 에디터 동기화 | state store/model 구현 |
| 변환 | 블록→코드 및 코드→블록 | generator/parser/decompiler |
| 실행 | simulator, hardware, sandbox | worker/runtime/target |
| 영속화 | 자동저장·복원·공유·파일 포맷 | storage/API/schema |
| 예외 | 지원하지 않는 문법·중간 오류·부분 실패 | 오류 분기/테스트/실험 |
| 안전·라이선스 | 사용자 데이터·외부 코드·저작권 | LICENSE/약관/보안 모델 |

## 조사 깊이 등급

- **L1 기능 인벤토리:** 공개 설명·문서 기반으로 기능과 화면을 구분
- **L2 코드/파일 지도:** 실제 공개 repo tree와 핵심 경로를 확인
- **L3 함수·이벤트 추적:** 버튼별 핸들러→상태→저장까지 소스 줄로 검증
- **L4 재현/측정:** 실제 화면·변환·예외·성능·모바일 시험

현재 공개 소스 10개는 **L2 + 일부 L3**, 나머지는 주로 **L1** 수준입니다. L4 실험은 아직 수행하지 않았습니다. 과장된 '완전 분석' 표기를 금지합니다.

## 기존 공통 분석

- [변환 엔진 아키텍처](../docs/02-architecture-patterns.md)
- [전환 UI/UX 패턴](../docs/03-ui-ux-patterns.md)
- [왕복 변환 벤치마크](../docs/04-roundtrip-benchmark.md)
- [증거 원장/분석 표준](../docs/05-evidence-ledger.md)
- [종합 비교](../docs/90-comparison-matrix.md)

## 소스와 비공개 제품을 구분하는 규칙

- 실제로 존재하는 파일/폴더명만 source map에 적습니다.
- 내부 구조의 추론은 **설계 가설**로 별도 표시합니다.
- 제품 화면의 버튼 이름/모달 문구/배치가 현행 제품에서 검증되지 않으면 '정확한 UI 미검증'으로 표시합니다.
- 같은 상표라도 target/장치/학습 코스/유료 플랜에 따라 기능이 다른 것은 개별 조건으로 기록합니다.
