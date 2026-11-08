# Scratch Editor — 블록 이벤트 → VM AST/runtime을 분리한 대규모 블록 네이티브 구조

> 기준일: 2026-11-08  
> 소스 기준: scratchfoundation/scratch-editor develop @ 50b077dc67f101cb92a76b843f8147c4541854b3  
> 관련 blocks: scratchfoundation/scratch-blocks develop @ ad11c884efb3b90d6c97a83213db9674e3766830  
> 분석 깊이: L2 + 일부 L3  
> 한 줄 판정: **Scratch는 사용자 텍스트 모드가 없는 block-native 환경이지만, workspace event를 VM 내부 block AST/state로 동기화하는 구조가 매우 분명해 에디터·실행기 분리의 기준 사례다.**

## 1. 왜 양방향 연구에 Scratch가 필요한가

Scratch 자체는 텍스트 전환 사례가 아니다. 그러나 block editor를 크게 운영할 때 필요한 문제를 잘 보여준다.

- GUI와 runtime 분리
- block event 동기화
- sprite별 실행 state
- stage/render/audio/asset storage 분리
- project loading/saving state machine
- extension integration

따라서 text parser보다 먼저 **block editor 자체의 production architecture**를 연구할 때 가치가 높다.

## 2. 현재 monorepo 구성

2026년 현재 scratch-editor monorepo의 packages에서 다음을 확인했다.

| package | 역할 |
|---|---|
| packages/scratch-gui | React 기반 editor UI |
| packages/scratch-vm | block program runtime/AST/state |
| packages/scratch-render | stage rendering |
| packages/scratch-storage | project/assets storage |
| packages/scratch-paint | costume editor |
| packages/scratch-svg-renderer | SVG rendering |
| packages/scratch-media-lib-scripts | media library tooling |
| packages/task-herder | shared task infrastructure |

별도로 scratch-blocks 저장소가 block workspace 구현을 담당한다.

## 3. 핵심 이벤트 흐름

공개 source에서 다음 연결을 직접 확인할 수 있다.

    scratch-blocks workspace
             │ change event
             ↓
    scratch-gui / containers/blocks.jsx
             │ addChangeListener
             ↓
    vm.blockListener(event)
             ↓
    editingTarget.blocks.blocklyListen(event)
             ↓
    VM target의 block AST/state
             ↓
        runtime execution

scratch-vm README도 VM이 scratch-blocks workspace의 event를 듣고 각 target의 block AST를 유지한다고 설명한다.

## 4. 중요한 오해 방지

VM 내부에 AST가 존재한다고 해서 text→blocks parser가 있는 것은 아니다.

Scratch의 AST는 **block workspace에서 오는 구조적 event를 실행 가능한 VM state로 유지하기 위한 것**이다. 이 점은 BlockMirror의 ‘Python parser AST → Blockly’와 완전히 다르다.

- Scratch: blocks → VM AST/state
- BlockMirror: Python text → parser AST → blocks
- MakeCode: text/blocks → compiler semantic layer → 다른 표현

AST라는 단어만 보고 양방향성을 추론하면 안 된다.

## 5. UI/UX 구조

제품 화면은 크게 다음 작업영역으로 나뉜다.

- code blocks palette/workspace
- stage
- sprite list와 sprite properties
- costumes editor
- sounds editor
- extension 추가
- project load/save/share 관련 shell

중요한 점은 editor UI가 모든 실행 로직을 직접 소유하지 않고 VM과 renderer/storage 등의 package에 역할을 분리한다는 것이다.

## 6. Project state와 runtime state 분리

scratch-gui의 reducer 구성에는 projectState가 별도 slice로 존재하고, project fetch/load 관련 HOC들이 loading state를 읽는다. 동시에 runtime program state는 scratch-vm이 관리한다.

이 분리는 교육용 editor에서 다음 상태를 한 객체에 몰아넣지 말아야 한다는 교훈을 준다.

- UI loading/modal state
- project persistence state
- code/block program state
- runtime/execution state
- rendering/media state

## 7. 복잡한 문제 해결 방식

### editor event와 VM의 decoupling
workspace 변화는 event contract를 통해 VM으로 전달된다. GUI가 runtime AST를 직접 조작하는 구조보다 테스트/교체가 쉽다.

### target별 block state
sprite/stage 같은 code-running object마다 block state가 있어, 하나의 전역 code string보다 Scratch의 object-oriented visual model에 자연스럽다.

### package 단위 역할 분리
GUI, VM, render, paint, storage를 독립 package로 두어 editor 기능 증가가 하나의 거대한 module에 집중되는 것을 줄인다.

## 8. 우리 설계에 가져갈 점

1. block editor와 execution engine 사이에 **명시적인 event/action boundary**를 둔다.
2. project load/save state와 runtime state를 분리한다.
3. UI component가 compiler/runtime 내부 객체를 직접 수정하지 않도록 한다.
4. text mode를 추가한다면 VM AST와 text AST를 같은 것으로 착각하지 말고 별도 conversion contract를 둔다.
5. 대규모 교육 도구에서는 asset/render/editor/runtime/storage를 각각 관찰 가능한 subsystem으로 나눈다.

## 9. 증거 원장 요약

| 주장 | 상태 | 근거 | 신뢰도 |
|---|---|---|---|
| scratch-editor monorepo package 구조 | 직접 확인 | current repo tree | A |
| blocks.jsx가 vm.blockListener를 workspace listener로 연결 | 직접 확인 | source | A |
| VM이 target block AST/state 유지 | 직접 확인 | virtual-machine.js + VM README | A |
| user text→blocks 기능 | 제공 근거 없음 | 제품/소스 구조 | A(부재 범위는 현재 사용자 제품 기준) |

## 10. 다음 L3/L4 검증

- block create/move/delete event가 VM blocks object에 반영되는 세부 sequence
- undo/redo와 VM event queue 경계
- project save/load 시 VM·GUI loading state 순서
- extension loading이 toolbox와 runtime primitive를 함께 갱신하는 방식
- 대형 project에서 event batching/performance
- offline/network failure와 autosave feedback

## Sources

- https://github.com/scratchfoundation/scratch-editor
- https://github.com/scratchfoundation/scratch-blocks
- 상세 파일 지도: [SOURCE-MAP.md](./SOURCE-MAP.md)
