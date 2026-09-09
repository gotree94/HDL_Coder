# HDL Coder 예제: Generate HDL Code from Stateflow Charts

> 공식 예제: **"Generate HDL Code from Stateflow Charts"**
> 목적: Stateflow 차트(상태 머신)로 **카운터를 모델링**하고, HDL 호환 차트 설정을 적용해 **합성 가능한 HDL 코드**를 생성.

## 개요
**Stateflow**(상태 머신/순차 로직)로 작성한 카운터 차트에 HDL 호환 설정을 적용하고 HDL Coder로 VHDL/Verilog 코드를 생성합니다. 상태 기반 순차 로직을 하드웨어로 구현하는 방법을 보여줍니다.

## 필요한 툴박스
- Stateflow
- HDL Coder
- Simulink

## Stateflow와 HDL
Stateflow는 상태 머신, 플로우차트, 시간 논리를 그래픽 언어로 모델링합니다.
- **순차 제어 로직**: 카운터, 시퀀서, 프로토콜 제어, 상태 판정
- HDL 생성 시 상태 머신이 **유한 상태 기계(FSM)** 로 합성
- HDL Coder는 Stateflow 차트를 지원

## 카운터 Stateflow 차트 예
상태 머신으로 만든 카운터:
```
[초기화] ─► [카운트업(CountUp)] ─► [리셋(Reset)] ─► ...
  - CountUp 상태: enable=1이면 count 증가
  - 최대값 도달 시 리셋/랩어라운드
```

### 차트 구성 요소
| 요소 | 역할 |
|------|------|
| **상태(state)** | 카운트 중 / 정지 / 리셋 등 |
| **전이(transition)** | 조건(enable, threshold)에 따른 상태 변화 |
| **동작(action)** | 카운트 증가, 출력 갱신 |
| **이벤트/타이머** | 주기적 실행(시간 로직) |

## HDL 호환 차트 설정
HDL 코드 생성을 위해 차트에 다음 설정 적용:
1. **HDL 호환 지시문/구성**: `chart.HDLCompatibleSettings` 또는 HDL 호환 템플릿
2. **데이터 타입**: 고정소수점 사용 (부동소수점은 native floating point 옵션)
3. **트리거/이벤트**: 클록(엣지) 기반 하드웨어 처리
4. **시간 로직**: HDL 지원(카운터/타이머) 형태로 변환
5. Stateflow에서 지원하지 않는 함수/구문 배제

```matlab
% HDL 호환 설정
chart = sfroot;  % 또는 모델의 차트 객체
% HDL 호환 템플릿 / 설정 적용 후 makehdl
makehdl('모델명/DUT');
```

## HDL 코드 생성
```matlab
open_system('StateflowCounterModel');
makehdl('StateflowCounterModel/Counter_Chart');   % 차트를 DUT로
```

## 하드웨어 의미
- Stateflow 상태 → **FSM 레지스터(state register)** 로 합성
- 상태 전이 조건 → **조합 논리(가산기·비교기)**
- 카운트 변수 → **레지스터 + 증가 로직**
- 이벤트/트리거 → **클록 엣지**

## 검증
- 시뮬레이션: Stateflow 차트 동작과 생성 HDL 시뮬레이션 결과 비교
- 카운트 시퀀스, 리셋, enable 동작 일치 확인

## 실행 요약
1. Stateflow로 카운터 차트 모델링
2. HDL 호환 차트 설정 적용
3. `makehdl`로 HDL 생성
4. 시뮬레이션으로 상태 머신 동작 검증

## 참고
- 공식 문서: Stateflow + HDL Coder → Generate HDL Code from Stateflow Charts (Open Live Script, 신규)
- 연관: Create HDL-Compatible Simulink Model, 카운터 HDL 예제
