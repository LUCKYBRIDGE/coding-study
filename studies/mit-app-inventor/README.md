# MIT App Inventor — 블록 네이티브 앱 제작 + 내부 YAIL 파이프라인

> 기준일: 2026-11-08  
> 소스 기준: mit-cml/appinventor-sources @ b097193903890d6aa729ac52464ae342d3a600e2  
> 분석 깊이: L2 + 일부 L3  
> 한 줄 판정: **사용자는 Designer와 Blocks Editor로 앱을 만들고, 내부에서는 YAIL 생성·Companion/Build Server 경로가 작동한다. 내부 textual IR이 존재하지만 사용자용 block↔text 양방향 IDE는 아니다.**

## 1. 서비스 목적과 사용자 흐름

App Inventor는 브라우저에서 앱 UI와 동작을 만드는 교육용 app builder다. 공식 서비스가 명시하는 핵심 화면은 **Designer**와 **Blocks Editor**다.

1. 프로젝트 생성/열기
2. Designer에서 화면에 component 배치
3. Blocks Editor에서 component event·logic 작성
4. Companion 또는 emulator/device로 live test
5. Build/export로 설치 가능한 앱 생성

이 구조에서 화면 설계와 행동 로직이 강하게 연결된다. component를 Designer에 추가해야 해당 component의 block API가 Blocks Editor에 나타나는 방식이 대표적이다.

## 2. 공개 저장소의 큰 모듈

| 모듈 | 역할 |
|---|---|
| appinventor/appengine | 브라우저 기반 app editor와 서버 측 project 기능 |
| appinventor/blocklyeditor | App Inventor용 Blockly 수정·확장 |
| appinventor/buildserver | build 요청과 앱 패키징 |
| appinventor/components | Android component runtime |
| appinventor/aiplayapp | Android Companion |
| appinventor/AICompanionApp / AIComponentKit | iOS Companion/component 계층 |
| appinventor/SchemeKit | iOS에서 사용하는 Scheme interpreter 계층 |

공식 App Inventor 소스 개요 문서에서도 이 모듈 구성이 설명된다.

## 3. UI/UX 구조

### Designer
- Palette: 사용 가능한 component 탐색
- Viewer: 실제 화면 조합
- Components: 선택·계층·이름 관리
- Properties: component 속성 수정

### Blocks Editor
- Built-in block categories
- 화면에 배치한 component별 blocks
- workspace
- block comment / context action
- warning/error 피드백
- backpack/copy-paste 등 프로젝트 편집 보조 기능(버전별 UI 차이는 실사용 재검증 필요)

### Connect / Live development
Companion 또는 emulator/device와 연결해 앱을 만들면서 확인하는 흐름이 학습 피드백을 짧게 만든다.

## 4. 핵심 데이터·실행 흐름

    Designer component model
             +
       Blockly workspace
             ↓
       YAIL generation
             ↓
       ┌────────────────┬─────────────────┐
       │ live companion │ build pipeline  │
       │ / interpreter  │ / build server  │
       └────────────────┴─────────────────┘
             ↓
         mobile app

공개 소스에서 GenerateYailAction / GenerateYailCommand, EditorManager.generateYailForBlocksEditors와 build server 연결을 확인할 수 있다.

## 5. ‘텍스트 코드가 있다’는 사실을 어떻게 해석해야 하나

App Inventor에서 YAIL은 매우 중요한 내부 생성 언어/중간 계층이지만 일반 학습자가 자유롭게 편집하는 기본 텍스트 코딩 모드는 아니다.

따라서:

- blocks → 내부 YAIL 생성: **O**
- YAIL 텍스트를 일반 사용자 IDE에서 수정: **기본 사용자 흐름 아님**
- 임의 YAIL/Java/Kotlin → 원래 blocks 복원: **일반 역변환 경로 아님**

즉 **내부 IR 존재와 dual-modality UX는 별개**다. 이 구분은 Scratch VM의 AST와 함께 중요한 비교 기준이다.

## 6. 얽힌 문제와 해결 방식

### UI component와 block API 동기화
Designer의 component instance/type 정보가 Blocks Editor에 전달되어 component-specific block을 구성한다. 단순한 고정 toolbox보다 앱의 UI 모델과 block language가 결합돼 있다.

### 빠른 실행과 정식 build를 분리
학습 중에는 Companion 기반의 짧은 피드백 루프를 제공하고, 배포 단계는 build server로 분리한다. ‘코드 작성→정식 APK build’를 매번 반복하지 않도록 한다.

### 프로젝트 저장과 build pipeline 분리
편집 중 프로젝트 상태와 생성/빌드 산출물을 별개 계층으로 둔다. 이는 교육용 서비스에서 autosave와 compile artifact를 분리할 때 참고 가치가 높다.

## 7. 교육/UI 관점에서 가져갈 점

1. **구성 요소 선택 → 사용할 수 있는 명령이 맥락적으로 나타나는 toolbox**.
2. 편집과 실행 사이 latency를 줄이는 **Companion/live preview** 구조.
3. event handler를 block shape와 category로 명시해 event-driven programming을 시각화.
4. 내부 IR을 사용자에게 억지로 노출하지 않고 학습 목표에 맞는 표현만 보여준다.
5. component rename과 block reference 같은 cross-editor consistency가 중요하다.

## 8. 한계와 주의

- App Inventor를 block↔text 전환 사례로 직접 분류하면 안 된다.
- source tree가 공개되어도 production infrastructure 전체가 동일하게 재현된다고 가정하지 않는다.
- 현행 버튼의 정확한 위치·반응형·계정/권한 흐름은 실제 브라우저 L4 검증 대상이다.
- YAIL이 내부에 있다는 이유로 S3/S4 왕복 등급을 주지 않는다.

## 9. 증거 원장 요약

| 주장 | 상태 | 근거 | 신뢰도 |
|---|---|---|---|
| Designer + Blocks Editor가 핵심 편집 화면 | 공식 확인 | App Inventor 공식 Designer/Blocks 문서 | A |
| appengine/blocklyeditor/buildserver/components 모듈 존재 | 직접 확인 | 공개 repo tree | A |
| GenerateYailCommand와 YAIL 생성 흐름 존재 | 직접 확인 | 공개 source search | A |
| block project의 자유로운 text→blocks 복원 | 근거 없음 | 공개 사용자 흐름/소스 경계 | D가 아니라 ‘지원 근거 없음’ |

## 10. 다음 L3/L4 검증

- Designer rename/add/remove → Blocks Editor toolbox/reference 변화 추적
- block edit → YAIL 생성 → Companion 전달까지 call chain 추적
- 저장 중 네트워크 단절·build failure의 사용자 피드백
- project import/export 파일 안의 screen/block state 버전 구조
- 태블릿에서 Designer/Blocks drag interaction 실험
- 큰 프로젝트에서 workspace loading/build latency 측정

## Sources

- https://appinventor.mit.edu/
- https://appinventor.mit.edu/explore/designer-blocks
- https://appinventor.mit.edu/blogs/evan/2023/04/03/overview-app-inventor-sources
- https://github.com/mit-cml/appinventor-sources
- 상세 파일 지도: [SOURCE-MAP.md](./SOURCE-MAP.md)
