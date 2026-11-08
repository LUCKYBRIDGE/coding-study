# 종합 비교표

> 조사 기준일: 2026-11-08  
> "역변환"은 **자유롭게 수정한 텍스트 → native blocks**를 의미합니다.

## 1. 핵심 기능

| 서비스 | 블록 | 텍스트 | 블록→텍스트 | 텍스트→블록 | 동시 비교 | 분류 |
|---|---:|---:|---:|---:|---:|---|
| MakeCode / PXT | O | TS/JS, target별 Python | O | O, 지원 subset | 주로 toggle | 제한적 완전 양방향 |
| Code.org App/Game Lab + Droplet | O | JavaScript | O | O | toggle | text-overlay 양방향 |
| Pencil Code | O | CoffeeScript/JS/CSS/HTML | O | O | toggle | text-overlay 양방향 |
| BlockPy / BlockMirror | O | Python | O | O | **O split** | AST 양방향 |
| EduBlocks | O | Python/HTML 등 | O realtime | 현재 근거상 X | O/유사 | editable one-way |
| Mind+ | O | Python/MicroPython 등 | O realtime | 현재 근거상 X | code area | editable one-way |
| mBlock 5 | O | Python 등 | O | 일반 역변환 미확인 | preview/tab | one-way + parallel IDE |
| PictoBlox | O | Python/C++ | hardware upload에서 O | X/미확인 | 별도 환경 | one-way + progressive |
| VEXcode Switch | O | Python | block→Switch/text | arbitrary reverse X | **한 화면 hybrid** | hybrid transition |
| Tinkercad Circuits | O | Arduino C++ | O realtime | pure text에서 일반 reverse X | Blocks+Text | editable one-way |
| Open Roberta | O | Python/Java/C/C++ generated | O | 일반 reverse X | generated source 중심 | multi-target generator |
| LEGO SPIKE Prime | O | Python | 동일 프로젝트 변환 미확인 | 미확인 | 별도 경험 | parallel/progressive |
| BlockPython 2026 | O | Python | 논문상 O | 논문상 O | 학습 단계형 | 연구 prototype |
| Blockly | O | 생성 대상 언어 문자열 | O generator | **X 기본 제공 아님** | host app 의존 | library baseline |
| MIT App Inventor | O | YAIL은 내부/generated 표현 | 내부 생성 O | 일반 reverse X | 사용자 text mode X | block-native + internal IR |
| Snap! | O | 사용자 text mode가 핵심 아님 | 해당 없음 | X | X | advanced block-native |
| Scratch Editor | O | X | 해당 없음 | X | X | block-native VM/AST |

## 2. 내부 구조

| 서비스 | 중심 구조 | canonical / semantic core | 미지원 표현 |
|---|---|---|---|
| MakeCode | compiler + decompiler | Static TypeScript 중심 | diagnostics / grey block 계열 |
| Droplet | text + structural markup | 원문 text token | text를 보존하며 구조화 |
| Pencil Code | Droplet | text | Droplet parser 범위 |
| BlockMirror | parser AST ↔ Blockly | Python source string | raw code block |
| EduBlocks | generator 중심으로 보임 | block state 추정 | reverse 근거 부족 |
| Mind+ | target code generator | block project 중심으로 보임 | text→block 일반 경로 미확인 |
| mBlock | target transcoder | block project + target template | text→block 일반 경로 없음 |
| PictoBlox | hardware upload generator + 별도 text env | environment별 상태 | block→Py Editor project migration 안 됨 |
| VEX Switch | heterogeneous block AST | block tree + Python text islands | text island로 유지 |
| Tinkercad | generator | block project → C++ | Text 전환 후 block 복구 안 함 |
| Open Roberta | Phrase/program model + visitors | NEPO/internal program model | text→NEPO decompiler 없음 |
| LEGO SPIKE | 별도 modalities | 제품 내부 비공개 | 변환 대상 아님 |
| BlockPython | 논문상 bidirectional engine | 상세 공개 부족 | 연구 문서 추가 확인 필요 |
| Blockly | workspace + CodeGenerator + serialization | block workspace | arbitrary text reverse는 host app이 구현 |
| MIT App Inventor | component model + blocks → YAIL → companion/build | project/block model | 내부 YAIL을 사용자 text mode로 보지 않음 |
| Snap! | block morph + Process/Context interpreter + serializer | block/project object model | text roundtrip이 목표가 아님 |
| Scratch Editor | scratch-blocks events → VM target block AST/state | block project + VM state | VM AST는 text parser AST가 아님 |

## 3. 구현 투명성

| 서비스 | 핵심 소스 공개 | 분석 가치 |
|---|---:|---|
| PXT | **O** | 매우 높음 |
| Droplet | **O** | 매우 높음 |
| Code.org | **O** | 매우 높음 |
| Pencil Code | **O** | 높음 |
| BlockMirror | **O** | 매우 높음 |
| Open Roberta | **O** | **매우 높음 — multi-target generator** |
| EduBlocks current core | 확인 어려움 | 중간 |
| Mind+ | 핵심 editor 비공개 | hardware UX 중심 |
| mBlock | 핵심 editor 비공개 | UX/extension 구조 중심 |
| PictoBlox | 핵심 editor 비공개 | progression / hardware UX 중심 |
| VEXcode | 비공개 | UX 매우 높음 |
| Tinkercad | 비공개 | UX 높음 |
| LEGO SPIKE | 비공개 | pedagogy progression 중심 |
| BlockPython | 논문 중심 | research design 높음 |
| Blockly | **O** | **매우 높음 — editor core 기준점** |
| MIT App Inventor | **O** | **매우 높음 — app builder + internal IR** |
| Snap! | **O** | **매우 높음 — block language/runtime** |
| Scratch Editor | **O** | **매우 높음 — GUI/VM/runtime 분리** |

## 4. 블록 네이티브 아키텍처 기준군

아래 4개는 text round-trip 점수로 줄 세우면 오히려 분석이 왜곡된다.

| 서비스 | 주로 볼 것 | 양방향 연구에 주는 교훈 |
|---|---|---|
| Blockly | workspace·event·serialization·generator | generator와 reverse parser는 별개 |
| MIT App Inventor | Designer↔Blocks↔YAIL↔Companion/build | 내부 textual IR ≠ 사용자 text modality |
| Snap! | custom blocks·Process/Context·serializer | 텍스트 전환 없이도 abstraction을 높일 수 있음 |
| Scratch Editor | block events↔VM target state·GUI/runtime/storage 분리 | 내부 AST ≠ source parser AST |

따라서 이 기준군은 아래 ‘주관적 비교 점수’의 역변환 점수 경쟁에서 제외한다.

## 5. 주관적 비교 점수

5점 만점. **제품 우열 점수가 아니라 이 연구 주제에서 참고 가치**입니다.

| 서비스 | 역변환 기술 | 원문 보존 | 전환 교육 UX | 구현 참고성 | 종합 연구 우선도 |
|---|---:|---:|---:|---:|---:|
| MakeCode/PXT | 5 | 3 | 4 | 5 | **5** |
| Droplet | 4 | **5** | 5 | 5 | **5** |
| BlockMirror | 4 | 3 | **5** | **5** | **5** |
| Pencil Code | 4 | 5 | 5 | 4 | **5** |
| Code.org App/Game Lab | 4 | 5 | 4 | 4 | **5** |
| VEX Switch | 1 | 4 | **5** | 2 | **4** |
| Open Roberta | 0 | 3 | 3 | **5** | **4** |
| EduBlocks | 1 | 3 | 4 | 2 | 3 |
| Mind+ | 1 | 2 | 4 | 2 | 3 |
| Tinkercad | 1 | 2 | 5 | 1 | 3 |
| mBlock | 1 | 2 | 4 | 2 | 3 |
| PictoBlox | 0 | N/A | 4 | 1 | 3 |
| LEGO SPIKE | 0 | N/A | 4 | 1 | 2 |
| BlockPython | 4* | 미확인 | 5 | 2 | 4 |

* 논문 보고 기준.

## 6. 목적별 1순위

### production block editor core
**Blockly / Scratch Editor**

### component 기반 app builder와 내부 IR
**MIT App Inventor**

### 고급 block language/runtime
**Snap!**

### compiler/decompiler 설계
**MakeCode / PXT**

### Python 직접 구현
**BlockMirror**

### text fidelity
**Droplet**

### block→text 학습 전환
**Pencil Code / Droplet**

### 점진적 scaffold
**VEXcode Switch**

### multi-target hardware code generation
**Open Roberta**

### hardware + code correspondence
**Tinkercad / Mind+ / mBlock**

### 수준별 환경 progression
**PictoBlox / LEGO SPIKE**

### learning analytics
**BlockPython 2026**

## 7. 우리가 만들 시스템을 가정한 추천 조합

한 제품을 복제하는 것보다 다음 조합이 더 좋습니다.

### 기술 core
BlockMirror식:
```
Python → AST → blocks
blocks → Python
```

### unsupported code
MakeCode식:
```
grey/raw text block
```

### transition UX
VEX식:
```
일부 block만 text island로 바꾸기
```

### 비교 학습
BlockPy식:
```
split view + 대응 highlight
```

### text fidelity
Droplet에서:
```
원문/source range를 최대한 보존
```

### hardware 확장
Open Roberta식:
```
core program model → target별 visitor/generator
```

### 수준별 UI
PictoBlox/SPIKE식:
```
같은 핵심 개념을 연령·숙련도에 따라 다른 coding environment로 노출
```

### 데이터
BlockPython식:
```
representation switch와 error process를 학습 evidence로 저장
```

이 조합이 현재 조사에서 가장 설득력 있습니다.
