# Open Roberta Lab — 서비스별 구조·화면·기술 심층 기록

> 기준: 2026-11-08 · 근거: 공개 monorepo 4,598개 파일의 실제 트리·visitor 코드 확인  
> 원본: [OpenRoberta/openroberta-lab](https://github.com/OpenRoberta/openroberta-lab) · [검토 커밋](https://github.com/OpenRoberta/openroberta-lab/tree/67acb8a7fe56cb5d04d90235f98a5df66ebd60db)  
> 주의: 아래 ‘화면’은 서비스에서 확인 가능한 주요 화면/모드의 **기능 단위 목록**입니다. 정확한 현행 URL, 화면 배치, 버튼 라벨을 모두 직접 클릭 검증했다는 뜻은 아닙니다.

## 1. 실제 프로젝트 파일·폴더

- `OpenRobertaWeb/src/app/roberta/controller/` — 화면 컨트롤
- `OpenRobertaWeb/src/app/configVisualization/` — 포트·배선 뷰
- `OpenRobertaWeb/src/app/nepostackmachine/` — 인터프리터/실행
- `OpenRobertaRobot/src/main/java/.../components/ProgramAst.java` — 프로그램 모델
- `OpenRobertaRobot/src/main/java/.../syntax/Phrase.java` — 기본 문장 표현
- `OpenRobertaRobot/src/main/java/.../visitor/lang/codegen/prog/AbstractPythonVisitor.java` — 공통 생성기
- `RobotMbed/.../MicrobitV2PythonVisitor.java` — micro:bit target
- `RobotSpike/.../SpikePythonVisitor.java` — SPIKE target
- `RobotCyberpi/.../Mbot2PythonVisitor.java` — mBot target
- `OpenRobertaServer/src/main/java/.../generated/restEntities/` — API DTO

**검증 가능한 원본 링크:** [OpenRobertaRobot/src/main/java/.../components/ProgramAst.java](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaRobot/src/main/java/.../components/ProgramAst.java) · [OpenRobertaRobot/src/main/java/.../syntax/Phrase.java](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaRobot/src/main/java/.../syntax/Phrase.java) · [OpenRobertaRobot/src/main/java/.../visitor/lang/codegen/prog/AbstractPythonVisitor.java](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/OpenRobertaRobot/src/main/java/.../visitor/lang/codegen/prog/AbstractPythonVisitor.java) · [RobotMbed/.../MicrobitV2PythonVisitor.java](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/RobotMbed/.../MicrobitV2PythonVisitor.java) · [RobotSpike/.../SpikePythonVisitor.java](https://github.com/OpenRoberta/openroberta-lab/blob/67acb8a7fe56cb5d04d90235f98a5df66ebd60db/RobotSpike/.../SpikePythonVisitor.java)

## 2. 화면/페이지 단위 인벤토리

1. **로봇/기기 선택**
2. **NEPO 블록 프로그램 편집**
3. **기기 구성·포트 설정**
4. **시뮬레이터/실행**
5. **생성한 소스 코드 확인**
6. **프로젝트 저장·관리**

**구분:** 로그인 여부·기기·target·학습 코스에 따라 화면이 달라질 수 있습니다. 각 URL과 팝업/모바일 화면은 실기기·브라우저 재현 검증이 필요한 항목입니다.

## 3. 버튼·행동 → 기술적 결과

| UI 행동 또는 기능 | 동작 및 연결 구조 | 증거 |
|---|---|---|
| 로봇 선택 | target별 block vocabulary/compiler visitor 전환 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 프로그램 편집 | Blockly/NEPO 모델 수정 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 기기 구성 | sensor/motor port와 hardware configuration 연계 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 시뮬레이션 | 웹 인터프리터/로봇 동작 연결 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 소스 표시 | NEPO model→target Python/Java/C++ generator | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| 저장·불러오기 | server REST entities/프로그램 데이터 모델 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |
| eval expression block | 블록 안의 text expression 처리 | 공개 소스·제품 설명 기반 / 실제 버튼 위치·문구는 별도 확인 |

버튼을 누른 뒤 **어떤 상태를 변경하고, 어떤 변환 작업·저장 작업이 발생하며, 언제 오류 또는 확인 대화상자가 나타나는지**를 별개의 검증 대상으로 취급합니다. 소스에 존재하는 기능과 현재 공개 UI에 노출되는 기능도 같다고 가정하지 않습니다.

## 4. 얽힌 문제와 실제 해결 접근

| 충돌 지점 | 구현 또는 설계에서 확인한 해결 접근 |
|---|---|
| 여러 기기의 다른 코드 생성 | 공통 프로그램 표현 + 기기별 visitor |
| 기기 포트와 코드의 불일치 | hardware configuration 모델과 generator 입력 연계 |
| 웹 실행과 실제 실행 차이 | 웹 시뮬레이터와 target 컴파일·업로드 경로 분리 |
| 시각 블록과 text expression 혼합 | eval expression처럼 부분 텍스트 해석 구역 제공 |

## 5. UI/UX 평가 프레임

- **발견성:** 전환 모드/기본 실행 버튼을 초보 학습자가 즉시 찾는가?
- **맥락 보존:** 전환 후 동일 문맥(선택 블록, 코드 줄, 스크롤 위치)이 유지되는가?
- **오류 복구:** 잘못 입력한 코드가 사라지는가, 보존되는가, 다시 편집할 수 있는가?
- **학습 지원:** 블록과 텍스트의 1:1 대응 또는 추상화 차이를 설명하는가?
- **작업 안정성:** 자동 저장·Undo·사본 생성·실행 중 편집·기기 연결이 충돌하지 않는가?
- **접근성:** 터치/키보드/화면 리더/작은 화면/한글 폰트가 대응되는가?

현재 수치 점수는 매기지 않았습니다. 이 항목들은 **브라우저 조작과 사용성 관찰**로 채워야 합니다.

## 6. 우리 설계에 참고할 핵심

기기별 코딩 서비스를 확장할 때 좋은 백엔드/컴파일러 모듈화 사례. 일반적인 텍스트→블록 디컴파일러로 오인하지 않는다.

## 7. 추가 검증 백로그

1. 화면별 실제 URL/메뉴 이동·반응형 차이 캡처
2. 모든 주요 버튼 클릭 전후 상태와 예외 조건 기록
3. 작은/큰 코드에 대한 변환 지연 및 Undo/Redo
4. 주석·오류·미지원 문법의 양방향 보존성 실험
5. 저장 포맷·프라이버시·라이선스 확인
6. 코드 근거에서 확인된 내부 상태 관리와 UI 이벤트를 연결한 sequence diagram

## 8. 분석 신뢰성

- **확인:** 위에 열거한 파일·폴더의 존재와 일부 공개 코드/설계 원리
- **설명/기능 수준:** 공개 문서와 기존 조사에 근거한 주요 화면·기능
- **미확인:** 모든 화면의 현재 버튼 좌표·버튼 명칭·클릭 결과·모바일 동작을 실제로 한 번씩 시험한 것은 아님
- **금지:** 공개 소스에 없는 백엔드 테이블명·내부 API·알고리즘을 추정해 실제 구조처럼 작성

관련 기존 상세 분석: [기존 Open Roberta Lab 기술 분석](../../docs/platforms/open-roberta.md)
