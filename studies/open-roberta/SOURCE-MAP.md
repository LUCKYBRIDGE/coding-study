# 소스코드 파일별 연결 지도 — OpenRoberta/openroberta-lab

> 근거: [확인한 Git 트리](https://github.com/OpenRoberta/openroberta-lab/tree/67acb8a7fe56cb5d04d90235f98a5df66ebd60db) · commit `67acb8a7fe56cb5d04d90235f98a5df66ebd60db`  
> 주의: 저장소 실제 파일명과 조사 목적 설명을 연결한 **소스 탐색 지도**이며 모든 함수 호출 관계를 정적 분석·실행 검증한 결과는 아님.

## 대표 모듈/페이지 구성

### 웹 UI

- [`OpenRobertaWeb/src/app/roberta/controller/program.controller.js`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaWeb/src/app/roberta/controller/program.controller.js)
- [`OpenRobertaWeb/src/app/configVisualization/confVisualization.ts`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaWeb/src/app/configVisualization/confVisualization.ts)
- [`OpenRobertaWeb/src/app/nepostackmachine/interpreter.interpreter.ts`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaWeb/src/app/nepostackmachine/interpreter.interpreter.ts)

### 프로그램 모델

- [`OpenRobertaRobot/src/main/java/de/fhg/iais/roberta/components/ProgramAst.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaRobot/src/main/java/de/fhg/iais/roberta/components/ProgramAst.java)
- [`OpenRobertaRobot/src/main/java/de/fhg/iais/roberta/syntax/Phrase.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaRobot/src/main/java/de/fhg/iais/roberta/syntax/Phrase.java)

### 언어별 출력

- [`OpenRobertaRobot/src/main/java/de/fhg/iais/roberta/visitor/lang/codegen/prog/AbstractPythonVisitor.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaRobot/src/main/java/de/fhg/iais/roberta/visitor/lang/codegen/prog/AbstractPythonVisitor.java)
- [`RobotMbed/src/main/java/de/fhg/iais/roberta/visitor/codegen/MicrobitV2PythonVisitor.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/RobotMbed/src/main/java/de/fhg/iais/roberta/visitor/codegen/MicrobitV2PythonVisitor.java)

### 장치별 출력

- [`RobotSpike/src/main/java/de/fhg/iais/roberta/visitor/spike/SpikePythonVisitor.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/RobotSpike/src/main/java/de/fhg/iais/roberta/visitor/spike/SpikePythonVisitor.java)
- [`RobotCyberpi/src/main/java/de/fhg/iais/roberta/visitor/Mbot2PythonVisitor.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/RobotCyberpi/src/main/java/de/fhg/iais/roberta/visitor/Mbot2PythonVisitor.java)

### 서버 API/데이터

- [`OpenRobertaServer/src/main/java/de/fhg/iais/roberta/generated/restEntities/InitRequest.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaServer/src/main/java/de/fhg/iais/roberta/generated/restEntities/InitRequest.java)
- [`OpenRobertaServer/src/main/java/de/fhg/iais/roberta/generated/restEntities/ListingRequest.java`](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaServer/src/main/java/de/fhg/iais/roberta/generated/restEntities/ListingRequest.java)

## UI 이벤트와 데이터 흐름

Blockly/NEPO 프로그램 및 기기 구성 → ProgramAst/Phrase → target별 visitor → Python/other target source → 실행/업로드. 별도로 browser stackmachine interpreter가 simulation을 처리한다.

## 소스에서 실재를 확인할 수 있는 기술적 쟁점

- **표시 상태:** 각 화면 컴포넌트 또는 컨트롤러는 어떤 editor와 toolbars를 그리는가
- **사용자 액션:** 이벤트가 어느 모델을 먼저 갱신하는가
- **변환 단계:** generator·parser·decompiler·visitor 중 실제 어느 경로를 타는가
- **오류 반환:** parse failure, generator failure, device error는 서로 다른가
- **영속화:** 저장하는 정본(source-of-truth)과 화면 상태가 구분되는가
- **Undo/Redo:** 소스 편집과 시각 블록 편집이 어떤 history를 사용하는가
- **연관 테스트:** 같은 오류를 다시 발생시키는 fixture가 있는가

## 내부적으로 얽힌 문제를 조사할 때의 순서

1. 실제 사용자 이벤트가 발생하는 파일을 찾는다.
2. 이벤트가 어떤 공유 모델·상태를 변경하는지 확인한다.
3. 모델에서 generator/parser/decompiler 호출 경로를 따라간다.
4. 성공/오류/부분 실패 분기와 원문 보존 방식을 확인한다.
5. UI에 결과가 다시 반영되는 이벤트와 저장·Undo 경계를 추적한다.
6. 모든 주장을 구체 파일·함수·테스트와 연결한다.

## 검증 한계

코드 생성 파이프라인의 실제 클래스 이름을 기반으로 한 요약이며, 언어별 역변환 parser를 입증하지 않는다.

### 다음 단계의 검증 작업

- 각 핵심 파일의 실제 함수·클래스와 호출 위치를 줄 번호 단위로 추출
- 화면별 버튼 event handler ↔ 내부 action 매핑
- 코드 전환 전후 스냅샷 및 회귀 테스트 실행
- 로그인·공유·저장·미리보기의 request/response 경로 분리
