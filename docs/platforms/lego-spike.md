# LEGO Education SPIKE

> 조사 기준일: 2026-11-08  
> 판정: **블록→텍스트 학습 progression은 강하지만 같은 프로그램의 양방향 변환 제품으로 보기는 어려움**  
> 신뢰도: **B**

## 1. 현재 coding modalities

공식 SPIKE App 자료는:
- icon blocks
- word blocks
- Python

을 포함한다고 설명합니다.

Scratch 기반 block coding에서 Python text coding까지 **점진적으로 능력을 발달**시키는 것이 명시적 목표입니다.

## 2. 제품 구분

중요:

- SPIKE Essential: Python programming 미지원
- SPIKE Prime: Python editor 사용 가능

따라서 "SPIKE는 Python 지원"을 전체 제품군에 일반화하면 틀립니다.

## 3. 변환 여부

현재 공식 FAQ와 Python 자료는:
- block coding
- Python text coding

을 모두 제공한다는 점은 확인하지만,

> 기존 block project를 Python으로 변환해 수정한 뒤 다시 동일 blocks로 되돌리는 round-trip

을 핵심 기능으로 설명하지 않습니다.

따라서 이 저장소에서는 **parallel/progressive modalities**로 분류합니다.

## 4. 교육적으로 주목할 점

양방향 변환이 없어도 progression 설계는 좋습니다.

```
Icon blocks
  ↓
Word blocks
  ↓
Python
```

이것은 representation jump를 한 번에 만들지 않고 중간 단계를 둡니다.

### Icon blocks
초기 독해 부담 최소화.

### Word blocks
명령의 언어적 의미를 더 직접적으로 노출.

### Python
실제 text syntax로 전환.

## 5. MakeCode와 비교

### MakeCode
동일 program을 서로 다른 representation으로 **변환**하는 데 강함.

### SPIKE
학습자의 skill progression에 맞춰 서로 다른 coding experience를 **제공**하는 데 강함.

둘은 교육적으로 다른 전략입니다.

## 6. 우리가 가져갈 것

양방향 editor를 만들더라도 다음 "표현 난이도 단계"는 유용합니다.

예:
1. pictorial block
2. labeled block
3. syntax-rich block
4. split block/text
5. text

즉 기술적으로는 하나의 AST여도 **UI representation density를 단계적으로 바꿀 수 있습니다.**

## 7. 하드웨어 교육에서 특히 중요한 점

표현 전환보다도:
- sensor input
- motor output
- physical result
- debugging

의 continuity가 더 중요할 수 있습니다.

따라서 block↔text 변환 시 program state뿐 아니라:
- device configuration
- ports
- calibration
- runtime state

가 유지되어야 합니다.

## 8. 한계

- true round-trip architecture 참고 자료로는 낮은 가치
- 내부 editor source가 공개되어 있지 않음
- Python 기능은 Prime 중심

## Sources

- SPIKE App download / progression  
  https://education.lego.com/ko-kr/downloads/spike-app/software/
- SPIKE App FAQ  
  https://education.lego.com/en-us/teacher-resources/lego-education-spike-prime/support-technical-info/lego-education-spike-prime-support-technical-info-product-info/
- SPIKE Prime Python  
  https://education.lego.com/en-us/product-resources/spike-prime/downloads/python/
