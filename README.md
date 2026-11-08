# coding-study

텍스트 코딩, 블록 코딩, 그리고 두 표현 사이의 변환을 지원하는 교육용 코딩 도구를 **제품 UI만 보지 않고 변환 구조·소스 코드·오류 처리·교육적 전환 설계까지** 분석한 저장소입니다.

> 조사 기준일: **2026-11-08**

## 서비스별 독립 심층 분석 (2026-11-08 확대)

**[studies/ 서비스별 분석 인덱스](studies/README.md)**에서 각 제품 폴더로 이동할 수 있습니다. 총 18개 서비스·엔진을 개별 폴더로 분리했으며, 공개 소스 확인이 가능한 10개는 실제 파일 경로를 따라가는 `SOURCE-MAP.md`를 추가했습니다. 화면·버튼·UX·기술적 상태 흐름과 실패 해결 방식을 정리하되, 직접 UI를 조작해 확인하지 않은 동작은 미검증으로 표시합니다.

## 먼저 결론

"블록과 텍스트를 모두 제공한다"와 "양방향 변환이 된다"는 같은 말이 아닙니다.

현재 확인한 구현은 크게 다음으로 갈립니다.

| 유형 | 대표 사례 | 핵심 |
|---|---|---|
| 컴파일러/디컴파일러형 양방향 | Microsoft MakeCode / PXT | Blocks·Python을 중간 의미 계층인 Static TypeScript 쪽으로 모으고 다시 Blocks/Python으로 디컴파일 |
| 텍스트 보존 오버레이형 양방향 | Droplet, Pencil Code, Code.org App Lab/Game Lab | 원문 텍스트를 정본으로 두고 소스 위치에 블록/소켓 마크업을 덧씌움 |
| AST 재구성형 양방향 | BlockPy / BlockMirror | Python → parser AST → Blockly XML, Blockly → Python generator |
| 편집 가능한 단방향 | EduBlocks, Mind+ | 블록 변화가 텍스트에 반영되고 텍스트 편집도 가능하지만 일반적인 text→blocks 역변환은 확인되지 않음 |
| 단방향 변환 + 텍스트 IDE | Tinkercad Circuits, mBlock 5, PictoBlox | 블록에서 C++/Python을 생성하거나 별도 텍스트 환경으로 넘어감 |
| 하이브리드 전환형 | VEXcode Switch | 블록 구조 안에 Python 텍스트 명령을 단계적으로 섞음 |
| 병렬/단계형 모달리티 | LEGO SPIKE Prime, PictoBlox | 블록과 Python을 모두 제공하지만 동일 프로그램의 왕복 변환은 핵심이 아님 |
| 다중 타깃 코드 생성 | Open Roberta Lab | NEPO/Blockly 프로그램을 robot별 Python/Java/C/C++ visitor로 생성 |
| 블록 네이티브/내부 IR 기준군 | Blockly, MIT App Inventor, Scratch, Snap! | 내부 AST·IR·generator가 있어도 사용자 text mode나 text→blocks 역변환과는 별개임을 보여주는 기준 사례 |
| 연구 프로토타입 | BlockPython (2026) | 양방향 블록↔Python에 학습 과정 증거와 AI 지원을 결합 |

가장 중요한 기술적 교훈은 **Blockly 자체에는 임의의 텍스트 코드를 블록으로 되돌리는 범용 기능이 없다는 것**입니다. Blockly 공식 문서는 블록에서 텍스트 코드를 생성하는 code generator와, 블록 워크스페이스 상태를 JSON/XML로 저장·복원하는 serialization을 서로 다른 기능으로 다룹니다. 진짜 역변환을 만들려면 별도의 parser/decompiler가 필요합니다.

## 분석 문서

### 공통 분석
- [분석 방법과 판정 기준](docs/00-method-and-criteria.md)
- [전체 지형과 비교 분류](docs/01-landscape.md)
- [양방향 변환 아키텍처 패턴](docs/02-architecture-patterns.md)
- [블록↔텍스트 UI/UX 패턴](docs/03-ui-ux-patterns.md)
- [Round-trip Benchmark](docs/04-roundtrip-benchmark.md)
- [증거 원장과 서비스별 분석 표준](docs/05-evidence-ledger.md)
- [종합 비교표](docs/90-comparison-matrix.md)
- [직접 설계할 때 가져갈 원칙](docs/99-design-takeaways.md)

### 개별 서비스 / 엔진
- [Microsoft MakeCode / PXT](docs/platforms/makecode-pxt.md)
- [Code.org App Lab · Game Lab / Droplet](docs/platforms/codeorg-droplet.md)
- [Pencil Code / Droplet](docs/platforms/pencil-code.md)
- [BlockPy / BlockMirror](docs/platforms/blockpy-blockmirror.md)
- [EduBlocks](docs/platforms/edublocks.md)
- [Mind+](docs/platforms/mindplus.md)
- [mBlock 5](docs/platforms/mblock5.md)
- [PictoBlox](docs/platforms/pictoblox.md)
- [VEXcode Switch](docs/platforms/vexcode-switch.md)
- [Tinkercad Circuits](docs/platforms/tinkercad-circuits.md)
- [Open Roberta Lab](docs/platforms/open-roberta.md)
- [LEGO Education SPIKE](docs/platforms/lego-spike.md)
- [BlockPython (2026 연구 프로토타입)](docs/platforms/blockpython-2026.md)
- [Blockly 자체를 기준점으로 보기](docs/platforms/blockly-baseline.md)
- [Blockly 독립 소스 분석](studies/blockly/README.md)
- [MIT App Inventor](studies/mit-app-inventor/README.md)
- [Snap!](studies/snap/README.md)
- [Scratch Editor](studies/scratch-editor/README.md)

## 빠른 선택 가이드

### "진짜 양방향" 구현을 연구하려면
1. **MakeCode / PXT** — 가장 완성도 높은 compiler/decompiler 계열
2. **Droplet** — 원문 텍스트 보존이 중요한 경우
3. **BlockMirror** — Python + Blockly 양방향을 직접 구현할 때 가장 읽기 좋은 참고 구조

### "초등·중등 학습 전환 UX"를 연구하려면
1. **VEXcode Switch** — 한 번에 전체 모달리티를 바꾸지 않고 블록 하나씩 텍스트화
2. **Pencil Code** — 블록 자체가 실제 텍스트 문법과 매우 가깝게 보임
3. **BlockPy** — 블록·텍스트 동시 화면(split view)
4. **MakeCode** — 같은 프로그램을 Blocks/JavaScript/Python 관점으로 오가게 함
5. **PictoBlox / SPIKE** — 연령·숙련도별 coding environment 단계화

### "블록 네이티브 아키텍처"를 연구하려면
1. **Scratch Editor** — workspace event → VM AST/state → runtime 분리
2. **MIT App Inventor** — Designer/component model → blocks → YAIL → live/build
3. **Snap!** — block-native interpreter와 고급 언어 추상화
4. **Blockly** — workspace/serialization/code generator의 책임 경계

### "하드웨어용 코드 생성 구조"를 연구하려면
1. **Open Roberta** — 공통 program model + robot별 language visitor
2. **mBlock 5** — target-specific upload transcoding
3. **Mind+** — block edit와 실시간 hardware code display
4. **Tinkercad** — Blocks → Blocks+Text → Text 단계

### 피해야 할 오해
- Blockly 사용 = 양방향 변환 지원 → **아님**
- 내부 AST/IR/YAIL 존재 = 사용자 텍스트 코딩 모드 존재 → **아님**
- VM 내부 AST 존재 = text parser/decompiler 존재 → **아님**
- Blocks + Text 동시 표시 = 텍스트 수정도 블록에 반영 → **아님**
- "Python 지원" = 기존 블록 프로젝트를 Python에서 수정하고 다시 블록으로 복원 → **제품마다 다름**
- "블록을 Python으로 변환" = Python editor로 프로젝트 전체가 이관됨 → **PictoBlox처럼 upload용 코드 생성만 되는 경우도 있음**
- 코드가 왕복되면 완전히 동일한 프로젝트가 복구됨 → **아님**. 블록 ID, 배치, 포맷, 주석 등은 손실될 수 있음

## 조사 방식

이 저장소는 단순 홍보 문구 요약이 아니라 다음 순서로 확인합니다.

1. 공식 웹/도움말에서 사용자가 실제로 가능한 동작 확인
2. 공개 저장소가 있으면 parser, decompiler, generator, sync model까지 소스 확인
3. 논문/연구에서 one-way, dual-modality, hybrid 분류 교차 확인
4. 확인된 사실과 추론을 분리
5. 재사용할 수 있는 설계 패턴과 실패 조건을 기록

각 문서 끝에 공식 문서와 공개 소스 링크를 남깁니다.
