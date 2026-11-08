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
| mBlock 5 | O | Python 등 | O | 일반 역변환 미확인 | preview/tab | one-way + parallel IDE |
| VEXcode Switch | O | Python | block→Switch/text | arbitrary reverse X | **한 화면 hybrid** | hybrid transition |
| Tinkercad Circuits | O | Arduino C++ | O realtime | pure text에서 X | Blocks+Text | editable one-way |
| LEGO SPIKE Prime | O | Python | 동일 프로젝트 변환 미확인 | 미확인 | 별도 경험 | parallel/progressive |
| BlockPython 2026 | O | Python | 논문상 O | 논문상 O | 학습 단계형 | 연구 prototype |

## 2. 내부 구조

| 서비스 | 중심 구조 | canonical / semantic core | 미지원 표현 |
|---|---|---|---|
| MakeCode | compiler + decompiler | Static TypeScript 중심 | diagnostics / grey block 계열 |
| Droplet | text + structural markup | 원문 text token | text를 보존하며 구조화 |
| Pencil Code | Droplet | text | Droplet parser 범위 |
| BlockMirror | parser AST ↔ Blockly | Python source string | raw code block |
| EduBlocks | generator 중심으로 보임 | block state 추정 | reverse 근거 부족 |
| mBlock | target transcoder | block project + target template | text→block 일반 경로 없음 |
| VEX Switch | heterogeneous block AST | block tree + Python text islands | text island로 유지 |
| Tinkercad | generator | block project → C++ | Text 전환 후 block 복구 안 함 |
| LEGO SPIKE | 별도 modalities | 제품 내부 비공개 | 변환 대상 아님 |
| BlockPython | 논문상 bidirectional engine | 상세 공개 부족 | 연구 문서 추가 확인 필요 |

## 3. 구현 투명성

| 서비스 | 핵심 소스 공개 | 분석 가치 |
|---|---:|---|
| PXT | **O** | 매우 높음 |
| Droplet | **O** | 매우 높음 |
| Code.org | **O** | 매우 높음 |
| Pencil Code | **O** | 높음 |
| BlockMirror | **O** | 매우 높음 |
| EduBlocks current core | 확인 어려움 | 중간 |
| mBlock | 핵심 editor 비공개 | UX/extension 구조 중심 |
| VEXcode | 비공개 | UX 매우 높음 |
| Tinkercad | 비공개 | UX 높음 |
| LEGO SPIKE | 비공개 | pedagogy progression 중심 |
| BlockPython | 논문 중심 | research design 높음 |

## 4. 주관적 비교 점수

5점 만점. **제품 우열 점수가 아니라 이 연구 주제에서 참고 가치**입니다.

| 서비스 | 역변환 기술 | 원문 보존 | 전환 교육 UX | 구현 참고성 | 종합 연구 우선도 |
|---|---:|---:|---:|---:|---:|
| MakeCode/PXT | 5 | 3 | 4 | 5 | **5** |
| Droplet | 4 | **5** | 5 | 5 | **5** |
| BlockMirror | 4 | 3 | **5** | **5** | **5** |
| Pencil Code | 4 | 5 | 5 | 4 | **5** |
| Code.org App/Game Lab | 4 | 5 | 4 | 4 | **5** |
| VEX Switch | 1 | 4 | **5** | 2 | **4** |
| EduBlocks | 1 | 3 | 4 | 2 | 3 |
| Tinkercad | 1 | 2 | 5 | 1 | 3 |
| mBlock | 1 | 2 | 4 | 2 | 3 |
| LEGO SPIKE | 0 | N/A | 4 | 1 | 2 |
| BlockPython | 4* | 미확인 | 5 | 2 | 4 |

* 논문 보고 기준.

## 5. 목적별 1순위

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

### hardware + code correspondence
**Tinkercad / mBlock**

### learning analytics
**BlockPython 2026**

## 6. 우리가 만들 시스템을 가정한 추천 조합

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

### 데이터
BlockPython식:
```
representation switch와 error process를 학습 evidence로 저장
```

이 조합이 현재 조사에서 가장 설득력 있습니다.
