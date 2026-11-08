# BlockPython (2026 research prototype)

> 조사 기준일: 2026-11-08  
> 판정: **최신 연구 흐름: 양방향 변환 자체보다 '변환 과정에서 학생이 어디서 막혔는가'를 데이터화**  
> 신뢰도: **C** — 2026 논문/프로토타입 보고 기반. production service와 공개 core source는 별도 검증 필요.

## 1. 왜 포함하나

2026년 8월 공개된 BlockPython 보고는 단순 block↔Python converter를 넘어:

- bidirectional translation
- learning process evidence
- deterministic diagnosis
- program visualization
- learning assistant

를 하나의 전환 시스템으로 결합합니다.

현재 "양방향 코딩 교육 도구"가 어디로 가는지 보기 좋은 최신 사례입니다.

## 2. 4단계 학습 흐름

논문은 다음 진행을 설명합니다.

1. Task Decomposition
2. Block-Based Practice
3. Code Challenge
4. Extended Interaction

핵심은 학생이 버튼 하나로 Blocks→Python을 보는 데 그치지 않고 **표현 사이의 대응을 스스로 재구성하도록 과제를 단계화**한다는 점입니다.

## 3. 양방향 변환을 학습 활동으로 사용

보고서 설명상 두 방향의 활동이 중요합니다.

### Python을 읽고 blocks로 재구성
학생이 text structure를 visual structure로 해석.

### blocks를 보고 Python 작성
학생이 이미 이해한 computational structure를 syntax로 표현.

즉 converter가 단순 convenience feature가 아니라 **representation translation 능력을 평가하는 장치**가 됩니다.

## 4. 과정 데이터

플랫폼은 다음 evidence를 수집한다고 설명합니다.

- block artifacts
- code versions
- run outcomes
- support usage
- dialogue

이 데이터로 오류가:
- computational concept 이해 부족인지
- Python syntax 표현 문제인지

를 구분하려 합니다.

## 5. 기존 도구와 다른 점

### MakeCode
변환 정확성과 제품 통합이 강점.

### BlockMirror
구현 구조와 split representation이 강점.

### VEX Switch
점진적 text exposure가 강점.

### BlockPython
**학습자가 표현을 변환하는 과정 자체를 추적·진단**하는 것이 강점.

## 6. 우리가 가져갈 아이디어

양방향 editor를 교육 서비스로 만든다면 단순히 "변환 성공"만 log하면 아깝습니다.

다음 event schema가 유용합니다.

```ts
type RepresentationEvent = {
  studentId: string;
  projectId: string;
  from: "blocks" | "text";
  to: "blocks" | "text";
  beforeHash: string;
  afterHash: string;
  diagnostics: Diagnostic[];
  unsupportedNodes: string[];
  editCountSinceLastSwitch: number;
  runPassed?: boolean;
  timestamp: number;
}
```

### 분석 가능한 것
- 학생이 block에서만 머무르는가
- text로 자주 갔다가 즉시 돌아오는가
- 어떤 syntax에서 변환 오류가 발생하는가
- converter를 본 뒤 직접 동일 syntax를 쓸 수 있는가
- run error가 concept error인지 syntax error인지

## 7. 과도한 AI 개입은 주의

학습 assistant가 자동으로 답을 주면 representation translation 연습이 사라질 수 있습니다.

권장:
1. deterministic parser/compiler diagnostics
2. structural hint
3. targeted question
4. 필요할 때만 생성형 설명

순서가 더 적절합니다.

## 8. 현재 한계

- 논문 기반 프로토타입
- 공개 서비스의 장기 운영/성능/접근성은 확인되지 않음
- bidirectional engine의 구체 source implementation은 MakeCode/BlockMirror만큼 공개적으로 검증하기 어려움

따라서 **제품 core를 복제하기보다 학습 분석 설계를 참고**하는 것이 좋습니다.

## Sources

- arXiv 2608.05716  
  https://arxiv.org/abs/2608.05716
- Author publication page  
  https://frank-haoming.github.io/publications/
