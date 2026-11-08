# Snap! 소스 지도

> 저장소: https://github.com/jmoenig/Snap  
> 검토 커밋: a7223934ead603e5cff3b5c59b1acc6a2f29541f

## UI / IDE

### src/gui.js
파일 상단의 constructor 목록에 IDE_Morph가 명시돼 있다. Snap! 전체 편집 환경의 핵심 UI entry로 추적할 가치가 높다.

### src/morphic.js
Snap! UI의 Morphic 기반. block만 보지 않고 drag/drop, dialog, widget 등의 interaction 기반을 조사할 때 사용한다.

### src/widgets.js
공통 widget/dialog UI 관련.

## Language / Blocks

### src/blocks.js
visual syntax/block morph 계층.

### src/byob.js
custom block / Build Your Own Blocks 관련 확장.

### src/objects.js
sprite/stage/object model.

## Runtime

### src/threads.js
파일 설명 자체가 ‘tail call optimized blocks-based programming language interpreter’이며 constructor 순서에 ThreadManager, Process, Context, Variable, VariableFrame, JSCompiler가 나타난다.

따라갈 핵심:
- script 시작 → Process
- Context stack
- cooperative scheduling
- block primitive dispatch
- stepping/error 처리

## Persistence

### src/store.js
SnapSerializer가 직접 정의된다.

### src/xml.js
XML serialization/parsing 지원.

### src/scenes.js
Project/Scene과 serializer 연결 흔적이 있다.

## Integration

### src/api.js
IDE_Morph prototype API가 노출된다.

### docs/Extensions.md
extension에서 JavaScript를 사용하는 방법과 IDE 접근이 설명되지만, 이를 native Snap! project의 JS roundtrip으로 해석하지 않는다.

## 분석 sequence

1. gui.js에서 사용자 action 찾기
2. blocks.js/byob.js의 syntax object 변화 확인
3. threads.js의 Process/Context 실행 경로 추적
4. objects.js에서 stage/sprite side effect 확인
5. store.js serializer가 project state를 어떻게 캡처하는지 확인
6. UI로 실행 상태가 어떻게 돌아오는지 확인

## 핵심 비교 포인트

Snap!은 **block-native interpreter + serializer** 구조라서 다음과 대조하기 좋다.

- MakeCode: compiler/decompiler
- Droplet: text-preserving structural editor
- BlockMirror: parser AST ↔ blocks
- Scratch: block events → VM target AST/runtime
