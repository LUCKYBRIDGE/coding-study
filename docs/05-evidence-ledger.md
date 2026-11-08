# 증거 원장과 서비스별 분석 표준

> 기준일: 2026-11-08  
> 목적: Craft Anything의 evidence-first 관점을 coding-study에 맞게 적용해 **확인한 사실, 공식 설명, 추론, 미확인을 섞지 않는다.**

## 1. 기본 원칙

이 저장소에서 분석의 최종 산출물은 ‘그럴듯한 설명’이 아니라 **주장과 근거의 연결**이다.

서비스마다 다음 흐름을 반복한다.

1. **Study** — 공식 제품·문서·source에서 관찰
2. **Model** — 화면, 상태, 변환, 실행, 저장 흐름을 구조화
3. **Check** — 실제 파일/함수/공식 동작과 모델을 대조
4. **Capture** — 확인 범위·모르는 부분·다음 검증을 문서에 남김

## 2. 주장 상태

| 상태 | 의미 | 허용 표현 |
|---|---|---|
| DIRECT_SOURCE | 공개 소스에서 파일/함수/경로 직접 확인 | “소스에서 확인됨” |
| OFFICIAL_BEHAVIOR | 공식 제품 문서에서 사용자 기능 확인 | “공식 문서 기준” |
| REPRODUCED | 실제 제품에서 동일 절차를 재현 | “실제 재현함” |
| RESEARCH | 논문/학술 자료에 보고됨 | “연구에서 보고됨” |
| INFERENCE | 여러 근거로 설계 구조를 추론 | “설계상 추정” |
| UNVERIFIED | 아직 확인하지 못함 | “미검증/확인 필요” |
| NO_EVIDENCE | 주장할 근거를 찾지 못함 | “지원 근거를 확인하지 못함” |

**NO_EVIDENCE는 ‘기능이 절대 없다’와 다르다.** 비공개 제품에서 특히 이 문구를 구분한다.

## 3. 최소 evidence record

서비스 문서의 중요한 주장에는 가능하면 다음을 남긴다.

| 필드 | 기록 내용 |
|---|---|
| claim | 검증하려는 한 문장 |
| status | 위 주장 상태 |
| source | 공식 URL 또는 repo/path |
| revision | commit SHA / release / 문서 확인일 |
| coverage | 어떤 환경·target·언어까지 확인했는지 |
| confidence | A/B/C/D |
| unknowns | 아직 모르는 것 |
| next_check | 다음 검증 절차 |

## 4. 파일 구조 표준

공개 소스가 있는 서비스:

    studies/<service>/
      README.md
      SOURCE-MAP.md

README.md에는 사용자가 경험하는 제품 구조와 기술 model을 연결한다.

- 한 줄 판정
- 조사 기준/commit
- 화면·페이지
- UI/action
- canonical state
- block→text
- text→block
- runtime
- persistence
- failure/recovery
- 교육적 설계
- 가져갈 점 / 가져오면 안 되는 점
- evidence summary
- 다음 L3/L4 검증

SOURCE-MAP.md에는 **실제로 존재하는 경로만** 적는다.

- repo + commit
- UI entry
- state/model
- parser/generator/decompiler
- runtime
- persistence
- tests/fixtures
- 호출 sequence
- 아직 추적하지 못한 부분

## 5. 서비스별 분석에서 금지할 것

- README 문구만 보고 내부 기술 스택을 확정
- Blockly를 쓴다는 이유만으로 text→blocks가 된다고 판단
- 내부 AST/IR이 있다는 이유로 사용자 text mode가 있다고 판단
- generated code가 보인다는 이유로 그 code를 수정해 blocks로 복원할 수 있다고 판단
- 공개 source path가 없는 제품에 가짜 파일명·DB schema 작성
- UI를 직접 열지 않았는데 버튼 위치·색·모달 문구를 현재 사실처럼 기록
- 여러 target 중 한 장치에서 되는 기능을 제품 전체 기능으로 일반화
- 최신 source의 존재만으로 production 배포가 그 commit과 동일하다고 단정

## 6. ‘내부 표현’과 ‘사용자 표현’을 반드시 분리

다음 네 층을 별도로 기록한다.

| 층 | 예 |
|---|---|
| User block representation | Blockly/Scratch workspace |
| User text representation | Python/JS editor |
| Internal semantic model | AST, IR, Phrase, VM block tree |
| Generated artifact | Python/C++ source, YAIL, bytecode, firmware |

예를 들어:

- App Inventor의 YAIL은 내부/generated layer이지 기본 사용자 text IDE가 아니다.
- Scratch VM의 AST는 block event 기반 runtime state이지 Python parser AST가 아니다.
- Blockly JSON serialization은 workspace 복원이지 source text 역변환이 아니다.

이 구분이 양방향 판정 오류를 크게 줄인다.

## 7. L3 추적 형식

버튼이나 action 하나를 깊게 볼 때 다음 sequence로 적는다.

    User Action
      → UI handler
      → state/model mutation
      → conversion/runtime call
      → persistence or side effect
      → success/error branch
      → visible feedback

가능하면 각 화살표에 실제 파일·함수·테스트를 붙인다.

## 8. L4 재현 형식

실제 제품 검증은 다음을 함께 남긴다.

- 날짜/버전
- OS/browser/device
- account/login 조건
- target/hardware
- 입력 sample
- click sequence
- before/after snapshot
- 변환 결과
- error/recovery
- 예상과 다른 점

## 9. 현재 저장소에 적용

기존 14개 연구에 더해 Blockly, MIT App Inventor, Snap!, Scratch Editor를 별도 서비스 폴더로 추가했다. 공개 source가 있는 항목은 SOURCE-MAP을 만들고, 비공개 제품은 source가 있는 것처럼 꾸미지 않는다.

이후 보강 작업은 ‘문서 길이 늘리기’보다 **evidence coverage를 L1→L2→L3→L4로 올리는 것**을 우선한다.
