# MIT App Inventor 소스 지도

> 저장소: https://github.com/mit-cml/appinventor-sources  
> 검토 커밋: b097193903890d6aa729ac52464ae342d3a600e2

## 큰 모듈

- appinventor/appengine — web editor + project/server logic
- appinventor/blocklyeditor — App Inventor용 Blockly layer
- appinventor/buildserver — package build
- appinventor/components — Android runtime components
- appinventor/aiplayapp — Android Companion
- appinventor/AICompanionApp — iOS Companion
- appinventor/SchemeKit — iOS Scheme interpreter

## UI ↔ Blocks 연결

### appinventor/appengine/src/com/google/appinventor/client/editor/blocks/BlocklyPanel.java
브라우저 editor와 Blockly panel을 연결하는 핵심 진입점 중 하나다.

### appinventor/blocklyeditor/src/blocklyeditor.js
App Inventor용 Blockly editor 동작과 context actions가 모인다. Generate YAIL context item도 검색으로 확인된다.

### appinventor/blocklyeditor/src/component.js
parent BlocklyPanel에서 component type/instance 정보를 받아 block editor에 반영하는 연결부가 확인된다.

### appinventor/blocklyeditor/src/workspace_svg.js
BlocklyPanel에서 호출되는 workspace 관련 App Inventor 확장 지점이 존재한다.

## YAIL 생성

- appinventor/appengine/src/com/google/appinventor/client/actions/GenerateYailAction.java
- appinventor/appengine/src/com/google/appinventor/client/explorer/commands/GenerateYailCommand.java
- appinventor/appengine/src/com/google/appinventor/client/editor/EditorManager.java
  - generateYailForBlocksEditors
- appinventor/blocklyeditor/tests/com/google/appinventor/blocklyeditor/yailgenerators.js
- appinventor/blocklyeditor/tests/README.md
  - YailGenerator.jar를 이용한 generator test 절차가 설명됨

이 경로들은 blocks가 내부 생성 코드로 내려가는 방향을 입증한다. 반대로 arbitrary YAIL을 native blocks로 되돌리는 일반 사용자 decompiler를 입증하지 않는다.

## Project / Build

- appinventor/appengine/src/com/google/appinventor/server/project/youngandroid/YoungAndroidProjectService.java
- appinventor/appengine/src/com/google/appinventor/server/ProjectServiceImpl.java
- appinventor/buildserver/src/com/google/appinventor/buildserver/BuildServer.java
- appinventor/buildserver/src/com/google/appinventor/buildserver/ProjectBuilder.java

YoungAndroidProjectService의 build 요청과 BuildServer 쪽 연결 흔적을 공개 코드에서 확인할 수 있다.

## 분석할 때 따라갈 sequence

1. Designer에서 component 변화
2. component model → BlocklyPanel/component.js
3. Blocks workspace의 component-specific API 갱신
4. block edit/save
5. GenerateYailCommand / EditorManager
6. live companion 또는 build server
7. 성공/실패 결과가 editor UI로 돌아오는 경로

## 주의

- 내부 YAIL을 ‘사용자 텍스트 모드’라고 표기하지 않는다.
- 프로젝트 파일 확장자와 내부 schema는 실제 importer/exporter 코드 및 fixture를 함께 확인한 뒤 확정한다.
- production backend의 비공개 운영 구성을 source tree만 보고 추정하지 않는다.
