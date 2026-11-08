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

### C. 단방향 + 별도 텍스트 IDE

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
- Text로 완전히 전환하면 되돌릴 수 없다는 경고가 알려져 있고, 공식 가이드는 C++ 자동 생성 중심
- **판정: S2**
- 신뢰도 B

### D. 하이브리드 전환

#### VEXcode Switch
- 블록의 구조를 유지한 채 특정 블록을 Python 텍스트 박스로 하나씩 바꿈
- 전체 프로그램을 한 번에 텍스트로 "던지는" 방식보다 점진적 전환
- arbitrary Python → 원래 블록 자동 복원 시스템은 아님
- **판정: 별도 H(하이브리드)**
- 신뢰도 B

### E. 블록·텍스트 병렬 제공

#### LEGO Education SPIKE
- Scratch 기반 아이콘/단어 블록
- SPIKE Prime은 Python editor 제공
- Essential은 Python 미지원
- 현재 공식 자료에서 같은 프로그램을 blocks↔Python으로 왕복하는 기능은 확인되지 않음
- **판정: 병렬 모달리티**
- 신뢰도 B

### F. 최신 연구 프로토타입

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

특히 2025~2026에는 **VEX Switch처럼 블록/텍스트를 "두 화면 중 하나"로 보지 않고 한 표현 안에 섞는 전환 설계**가 눈에 띕니다. 따라서 현재 분석에서는 one-way / dual-modality만으로는 부족하고 **hybrid transition**을 별도 축으로 둡니다.

## 가장 연구 가치가 높은 4개

### 1. MakeCode / PXT
"어떻게 compiler architecture로 양방향을 제품급으로 만드는가"를 보기 가장 좋습니다.

### 2. Droplet
"어떻게 원문 텍스트를 잃지 않으면서 블록처럼 편집하게 만드는가"를 보기 좋습니다.

### 3. BlockMirror
"Python AST를 Blockly로 직접 변환하는 구현을 어떻게 쪼개는가"를 보기 좋습니다.

### 4. VEX Switch
"완전한 양방향 변환 없이도 학습 전환을 더 잘 만들 수 있는가"를 보여줍니다.

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
