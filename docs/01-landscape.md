# 전체 지형과 비교 분류

> 조사 기준일: 2026-11-08

## 핵심 분류

### A. 실질적 양방향 / dual-modality

#### Microsoft MakeCode / PXT
- Blocks ↔ Static TypeScript: 강한 양방향
- Python을 지원하는 target에서는 Python도 Static TypeScript 경유
- compiler/decompiler가 실제 제품 핵심
- 미지원 표현은 grey/raw 계열 블록으로 보존하는 설계가 일부 존재
- **판정: S4에 가까운 제한적 의미 왕복**
- 신뢰도 A

#### Code.org App Lab / Game Lab + Droplet
- JavaScript를 typed text 또는 Droplet blocks로 편집
- Droplet은 "블록 AST를 텍스트로 출력"하는 일반 Blockly 모델과 다름
- 원문 텍스트 토큰을 유지하면서 source range 위에 block/socket/indent 마크업을 생성
- **판정: 텍스트 보존 중심 S3~S4**
- 신뢰도 A

#### Pencil Code + Droplet
- CoffeeScript 중심, JavaScript/CSS/HTML도 제공
- block/text 토글이 제품의 핵심
- 텍스트 원문과 블록 표현의 거리가 매우 짧음
- **판정: S3~S4**
- 신뢰도 A

#### BlockPy / BlockMirror
- Python 블록·텍스트·split view
- Python → Skulpt parser/AST → Blockly
- Blockly → Python generator
- 파싱 실패/미지원 부분을 raw code block으로 보존
- **판정: Python subset의 S4**
- 신뢰도 A

### B. 편집 가능한 단방향

#### EduBlocks
- 블록마다 한 줄의 Python/HTML 등에 가깝게 대응
- 블록 변경 시 텍스트가 실시간 갱신
- 교육적으로 block→syntax 대응이 명료
- 기존 연구에서는 text edit → blocks 복귀를 지원하지 않는 editable one-way로 분류
- **판정: S2**
- 신뢰도 B/C

#### Mind+ (DFRobot)
- block-based programming과 code를 한 workspace에서 연결
- 공식 사이트가 "Blocks auto-generate code"를 명시
- 하드웨어 튜토리얼에서 block drag/drop → code text area 자동 생성 구조 확인
- 일부 code mode에서 manual editing 가능
- 자유 수정한 text → native block 역변환 근거는 현재 확인되지 않음
- **판정: S2**
- 신뢰도 B

### C. 단방향 + 별도 텍스트 IDE / 환경

#### mBlock 5
- Scratch 3 계열 블록
- 여러 하드웨어의 Upload 모드에서 블록을 Python/C/JavaScript로 transcoding
- "corresponding Python statements" 뷰와 별도 Python Editor 제공
- 공식 자료에서 자유 수정한 Python을 기존 블록 프로젝트로 역변환한다는 근거는 확인되지 않음
- 제품·장치·모드에 따라 Python UX가 다름
- **판정: S1~S2 + 병렬 텍스트 IDE**
- 신뢰도 B

#### Tinkercad Circuits
- Arduino: Blocks / Blocks + Text / Text
- Blocks + Text에서 C++는 블록 변화에 따라 즉시 갱신
- pure Text 전환은 code-first graduation 성격이며 일반 text→blocks decompiler가 아님
- **판정: S2**
- 신뢰도 B

#### PictoBlox
- Junior Blocks / Blocks / Py Editor / Py Notebook / Embedded C++를 별도 environment로 제공
- 공식 FAQ는 hardware programming에서 block→Python/C++ 변환을 지원한다고 설명
- 동시에 block code가 Python environment로 변환되는 것은 아니라고 명시
- **판정: S1~S2 + 병렬/단계형 환경**
- 신뢰도 B

### D. 다중 타깃 코드 생성

#### Open Roberta Lab
- visual language NEPO/Blockly 기반
- 내부 Phrase/program model에서 robot별 Python/Java/C/C++ visitor로 source 생성
- 공개 repo에 공통 AbstractPythonVisitor와 Microbit/Spike/mBot2/EV3 등 target visitor가 존재
- 일반 Python/C++ → NEPO blocks 역변환 decompiler는 확인되지 않음
- expression 수준 text island인 `eval .. as ...` block도 제공
- **판정: generator/compiler pipeline**
- 신뢰도 A

### E. 하이브리드 전환

#### VEXcode Switch
- 블록의 구조를 유지한 채 특정 블록을 Python 텍스트 박스로 하나씩 바꿈
- 전체 프로그램을 한 번에 텍스트로 "던지는" 방식보다 점진적 전환
- arbitrary Python → 원래 블록 자동 복원 시스템은 아님
- **판정: 별도 H(하이브리드)**
- 신뢰도 B

### F. 블록·텍스트 병렬/단계형 제공

#### LEGO Education SPIKE
- Scratch 기반 아이콘/단어 블록
- SPIKE Prime은 Python editor 제공
- Essential은 Python 미지원
- 현재 공식 자료에서 같은 프로그램을 blocks↔Python으로 왕복하는 기능은 확인되지 않음
- **판정: 병렬 모달리티**
- 신뢰도 B

#### PictoBlox
- 연령대에 따라 Junior Blocks → Blocks → Py Editor/Py Notebook의 progression
- same-program round-trip보다는 환경 자체를 단계화
- **판정: progressive modalities**
- 신뢰도 B

### G. 블록 네이티브 / 내부 표현 기준군

이 그룹은 block↔text 전환 사례가 아니라 **“내부 표현이 있다는 것”과 “사용자에게 두 표현을 제공한다는 것”을 분리하기 위한 기준군**이다.

#### Blockly
- workspace serialization과 code generator는 분리된 기능
- block→source string 생성은 제공하지만 arbitrary source→blocks parser/decompiler는 기본 제공하지 않음
- **판정: block editor/library baseline**
- 신뢰도 A

#### MIT App Inventor
- Designer component model + Blocks Editor
- blocks에서 YAIL 생성 후 Companion/live 또는 build pipeline으로 연결
- YAIL은 내부/generated 표현이며 기본 사용자 text IDE가 아님
- **판정: block-native + internal IR/codegen**
- 신뢰도 A

#### Snap!
- block-native interpreter와 project serializer
- first-class lists/procedures/continuations, custom blocks로 블록 언어 자체의 추상화를 확장
- text로 전환하지 않고도 고급 CS 개념을 제공하는 대안 사례
- **판정: advanced block-native language**
- 신뢰도 A

#### Scratch Editor
- scratch-blocks workspace event → VM target block AST/state → runtime
- VM AST는 text parser AST가 아니라 block-native execution state
- GUI/VM/render/storage를 package 단위로 분리
- **판정: block-native VM architecture**
- 신뢰도 A

### H. 최신 연구 프로토타입

#### BlockPython (2026)
- 논문상 blocks ↔ Python bidirectional translation
- 단순 변환에 그치지 않고 학습자의 block artifact, code version, 실행 결과, 지원 사용 기록을 과정 데이터로 축적
- task decomposition → block practice → code challenge → extended interaction의 단계적 전환
- 공개 제품/소스 상태는 별도 검증 필요
- **판정: 연구 단계 S3~S4 주장**
- 신뢰도 C

---

## 2023 연구 분류와 2026 현재의 변화

Israel-Fishelson et al.은 도구를 다음처럼 구분했습니다.

- read-only one-way: Blockly, mBlock 등
- editable one-way: EduBlocks
- dual-modality: BlockPy 등

이 분류는 지금도 유용하지만 제품이 바뀌었습니다.

2025~2026에는 세 가지 변화가 더 뚜렷합니다.

1. **VEX Switch**처럼 block/text를 같은 표현 안에 섞는 hybrid transition
2. **PictoBlox / SPIKE**처럼 연령·숙련도에 따라 coding environment 자체를 단계화
3. **Open Roberta / Mind+ / mBlock**처럼 hardware target별 code generation을 학습 연결고리로 활용

따라서 현재 분석에서는 one-way / dual-modality만으로는 부족하고 **hybrid, progressive modalities, multi-target generation, block-native/internal-IR baseline**을 별도 축으로 둡니다.

## 양방향·전환 관점에서 가장 연구 가치가 높은 5개

### 1. MakeCode / PXT
"어떻게 compiler architecture로 양방향을 제품급으로 만드는가"를 보기 가장 좋습니다.

### 2. Droplet
"어떻게 원문 텍스트를 잃지 않으면서 블록처럼 편집하게 만드는가"를 보기 좋습니다.

### 3. BlockMirror
"Python AST를 Blockly로 직접 변환하는 구현을 어떻게 쪼개는가"를 보기 좋습니다.

### 4. VEX Switch
"완전한 양방향 변환 없이도 학습 전환을 더 잘 만들 수 있는가"를 보여줍니다.

### 5. Open Roberta
"하나의 교육용 program model을 여러 robot·언어 target으로 어떻게 확장하는가"를 보기 좋습니다.

## 별도 아키텍처 기준군

양방향 자체의 5개 우선순위와 별개로, production block editor 구조를 이해하려면 **Blockly / Scratch Editor / MIT App Inventor / Snap!**을 함께 본다. 특히 ‘AST/IR이 있으니 text→blocks도 될 것’이라는 추론을 막는 데 유용하다.

## 기준점: Blockly

Blockly는 매우 훌륭한 block editor이지만 **범용 text→blocks parser가 아닙니다**.

공식 문서가 제공하는 핵심은:
- workspace blocks → language-specific source string
- JSON/XML workspace serialization
- JavaScript, Python, Lua, Dart, PHP generator

따라서 양방향 도구를 만들 때는 Blockly 위에 parser/decompiler 계층을 추가해야 합니다.

공식:
- https://docs.blockly.com/guides/create-custom-blocks/code-generation/overview/
- https://docs.blockly.com/guides/configure/serialization/
