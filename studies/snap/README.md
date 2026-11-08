# Snap! — 텍스트 전환 없이 블록 언어 자체의 추상화를 확장한 사례

> 기준일: 2026-11-08  
> 소스 기준: jmoenig/Snap @ a7223934ead603e5cff3b5c59b1acc6a2f29541f (v12.3.11 준비 커밋)  
> 분석 깊이: L2 + 일부 L3  
> 한 줄 판정: **Snap!은 block↔text 변환기가 아니라, first-class procedures·lists·continuations와 custom blocks로 블록 언어 자체를 고급 CS 개념까지 확장한 환경이다.**

## 1. 왜 이 연구에 필요한가

‘블록을 배우고 나면 텍스트로 넘어가야 한다’는 설계만 보면 전환 문제를 과대평가할 수 있다. Snap!은 다른 방향을 보여준다.

- procedures를 값처럼 다루기
- higher-order programming
- user-defined control structure
- first-class lists
- continuations
- custom blocks

즉 **표현을 텍스트로 바꾸지 않고도 언어의 추상화 수준을 크게 높일 수 있다.**

## 2. 주요 화면/UX

Snap!의 IDE는 한 화면에 다음을 조직한다.

- block palette/category
- scripting area
- stage
- sprite/scene 관련 UI
- variable/list watchers
- custom block editor
- project/library import/export
- 실행/중지/stepping과 runtime feedback

정확한 최신 버튼 배치와 모바일 동작은 L4 대상이지만, IDE·block morph·runtime이 동일 공개 코드베이스에 있어 UI에서 실행까지 연결 추적이 가능하다.

## 3. 실제 공개 파일 구조

| 파일 | 역할 |
|---|---|
| src/gui.js | IDE_Morph와 주요 편집 환경 |
| src/blocks.js | block/syntax morph 계층 |
| src/byob.js | Build Your Own Blocks / custom block 계층 |
| src/threads.js | ThreadManager, Process, Context, JSCompiler 등 실행 계층 |
| src/objects.js | sprite/stage/object model |
| src/morphic.js | Morphic UI 기반 |
| src/store.js | SnapSerializer와 project serialization |
| src/xml.js | XML parsing/serialization support |
| src/api.js | IDE API |
| src/scenes.js | scene/project 관련 model |

## 4. 실행 모델

    visual block morphs
           ↓
      Process / Context
           ↓
    cooperative threads
           ↓
     sprite/stage state

threads.js는 스스로를 ‘blocks-based programming language interpreter’라고 설명하며 ThreadManager, Process, Context 등을 정의한다. 즉 코드 생성 후 외부 언어를 실행하는 방식보다 **블록 프로그램 자체를 해석하는 runtime**에 가깝다.

## 5. 저장 모델

SnapSerializer와 XML serializer를 통해 project/scene/block state를 보존한다. 이는 텍스트 source를 canonical representation으로 쓰는 Droplet과 대조적이다.

- Droplet: text가 중심이고 구조 마크업을 얹음
- Snap!: block/project object model이 중심
- MakeCode: compiler/decompiler semantic layer를 중심으로 여러 표현 연결

이 세 가지를 같이 보면 canonical representation 선택이 제품 UX를 어떻게 제약하는지 비교하기 좋다.

## 6. JavaScript가 보인다고 text mode는 아니다

Snap! 코드베이스와 extension 문서에는 JavaScript 연결이 있지만, 이것을 ‘같은 프로젝트의 블록↔JavaScript 전환’으로 해석하면 안 된다.

- 구현 언어가 JavaScript인 것
- extension/plugin에서 JavaScript를 쓰는 것
- 사용자의 native Snap! program을 arbitrary JS와 왕복하는 것

은 서로 다른 문제다.

## 7. 얽힌 문제 해결 방식

### 문법 오류를 줄이면서 고급 개념 제공
block shape와 slot type이 syntactic affordance를 제공해, 텍스트 문법 오류 없이 higher-order concept를 다룬다.

### 사용자 정의 언어 확장
custom block을 통해 새로운 procedure/control abstraction을 사용자가 언어 내부에서 만든다. 제품 개발자가 모든 고급 기능을 미리 block category로 하드코딩할 필요가 줄어든다.

### 실행 상태의 시각적 연결
block execution/stepping과 stage 변화가 같은 IDE 안에서 이어져 novice debugging에 유리하다.

## 8. 우리 설계에 가져갈 점

1. ‘텍스트 전환’ 자체를 목표로 두기 전에 **블록에서 어느 추상화까지 학습할지** 정의한다.
2. custom block/function abstraction을 일찍 제공하면 거대한 toolbox 문제를 줄일 수 있다.
3. block runtime과 UI의 실행 강조(highlight/stepping)는 debugging 교육에 중요한 별도 시스템이다.
4. text conversion이 없는 제품도 language/runtime architecture 참고 가치가 매우 높다.

## 9. 증거 원장 요약

| 주장 | 상태 | 근거 | 신뢰도 |
|---|---|---|---|
| first-class lists/procedures/continuations | 공식 확인 | Snap! About | A |
| IDE_Morph, Process, SnapSerializer 구조 | 직접 확인 | 공개 source | A |
| block→text user mode | 확인되지 않음/핵심 아님 | 공식 제품 구조 | A |
| JS extension = block↔JS roundtrip | **아님** | 기능 경계 비교 | A |

## 10. 다음 L3/L4 검증

- block click/run → Process 생성 → execution highlight call chain
- custom block 정의 저장 schema
- serializer의 block ID/reference 보존 방식
- large project loading과 watcher 성능
- keyboard/touch 접근성 및 작은 화면
- Snap!의 JSCompiler가 어떤 용도에서 interpreter와 분기되는지 함수 단위 추적

## Sources

- https://snap.berkeley.edu/about
- https://github.com/jmoenig/Snap
- 상세 파일 지도: [SOURCE-MAP.md](./SOURCE-MAP.md)
