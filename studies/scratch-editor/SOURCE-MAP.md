# Scratch Editor 소스 지도

> scratch-editor: develop @ 50b077dc67f101cb92a76b843f8147c4541854b3  
> scratch-blocks: develop @ ad11c884efb3b90d6c97a83213db9674e3766830

## Monorepo packages

- packages/scratch-gui
- packages/scratch-vm
- packages/scratch-render
- packages/scratch-storage
- packages/scratch-paint
- packages/scratch-svg-renderer
- packages/scratch-media-lib-scripts
- packages/task-herder

## Blocks UI → VM

### packages/scratch-gui/src/containers/blocks.jsx
workspace에 vm.blockListener를 change listener로 붙이는 코드가 확인된다.

### packages/scratch-gui/src/lib/blocks.js
scratch-blocks와 VM을 연결하는 adapter 역할을 확인할 수 있다.

## VM

### packages/scratch-vm/src/virtual-machine.js
VirtualMachine class가 blocks, stage, extensions 사이 연결을 담당한다.

확인된 핵심 method:

- blockListener(e)
- editingTarget.blocks.blocklyListen(e) 호출

### packages/scratch-vm/README.md
VM이 scratch-blocks event를 수신해 target별 block AST/state를 유지한다는 architecture 설명이 있다.

## GUI state

### packages/scratch-gui/src/reducers/gui.ts
projectState를 포함한 GUI reducer 구성이 확인된다.

### packages/scratch-gui/src/lib/vm-manager-hoc.jsx
projectState의 loading/projectData/projectId와 VM 초기화 흐름을 연결하는 지점.

### packages/scratch-gui/src/lib/project-fetcher-hoc.jsx
project loading state에 따라 fetch/show 흐름을 관리한다.

### packages/scratch-gui/src/containers/project-watcher.jsx
현재 project state와 change 관찰 관련 지점.

## 분석 sequence

1. blocks.jsx에서 Blockly/ScratchBlocks event 발생
2. VM blockListener
3. target.blocks.blocklyListen
4. VM block container/AST 변화
5. runtime scheduler/primitive 실행
6. renderer/audio/monitor 업데이트
7. project watcher/storage가 persistence 변화 감지

## text conversion 연구에서의 의미

이 source map은 **AST가 존재해도 text parser가 아닐 수 있음**을 확인하는 기준 자료다. Scratch VM AST는 block event에서 만들어지는 실행 state이며, 임의 source text를 native blocks로 역변환하는 계층과 다르다.

## 다음 L3 후보

- scratch-vm의 blocks container 구현
- runtime sequencer/thread scheduler
- project serialization 입출력
- extension manager와 toolbox refresh
- event queue 및 undo/redo interaction
