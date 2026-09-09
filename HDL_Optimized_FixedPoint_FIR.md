# HDL Coder 예제: Optimized Fixed-Point FIR Filters

> 공식 예제: **"Optimized Fixed-Point FIR Filters"**
> 목적: **고정소수점 FIR 필터**를 필터 응답 특성(저지대역 감쇠, 비트 수)에 맞춰 최적화.

## 개요
고정소수점 FIR 필터를 설계할 때 **필터 응답의 특성**(예: 저지대역 감쇠, 특정 사양 달성에 필요한 비트 수)을 고려하여 최적화합니다. 하드웨어 설계자는 흔히 **계수개수(탭)를 늘리거나 비트수를 줄이는 트레이드오프**를 통해 ASIC/FPGA 성능을 최적화합니다. 이 예제는 그러한 고정소수점 필터 최적화 과정을 보여줍니다.

## 필요한 툴박스
- DSP System Toolbox
- DSP HDL Toolbox (HDL 생성 시)
- Fixed-Point Designer
- HDL Coder
- Simulink

## 고정소수점 필터 최적화의 원리

### 왜 고정소수점인가?
- FPGA/ASIC에서는 고정소수점 연산이 하드웨어 자원·속도·전력에서 압도적 우위
- 부동소수점은 DSP 블록을 많이 사용하고 면적/전력 큼

### 최적화 트레이드오프
| 방법 | 효과 |
|------|------|
| 탭 수 증가 | 필터 응답(저지대역 감쇠) 개선, **승산기 증가** |
| 계수/데이터 비트 수 감소 | 하드웨어 자원(폭) 감소, **정밀도 저하** |
| 계수 대칭 활용 | 승산기 수 절반으로 감소 |
| CSD 적용 | 승산기를 시프트-덧셈으로 대체해 면적 감소 |

**핵심 요점**: "더 많은 계수 + 더 적은 비트" 또는 "더 적은 계수 + 더 많은 비트" 사이에서 사양을 만족하면서 하드웨어를 최소화하는 조합을 찾는 것.

## 최적화 흐름

### 1. 필터 설계 (DSP System Toolbox)
```matlab
% 원하는 응답 사양으로 FIR 계수 설계
order = 40;                    % 탭 수 (훨씬 많게)
Fc = 0.4;                      % 정규화 차단 주파수
b = fir1(order, Fc);           % 계수 설계
```

### 2. 고정소수점 변환 (Fixed-Point Designer)
- 계수·입력·내부 데이터의 **부호, 정수 비트, 소수 비트** 결정
- `fi` 객체로 고정소수점 타입 지정
- 양자화 오차가 필터 응답(저지대역 감쇠 등)에 미치는 영향 분석

### 3. 응답 특성에 따른 비트 폭 최적화
- 필터의 저지대역 감쇠 요구치를 만족하도록 **소수 비트 수 최소화**
- 지수적으로 감소하는 계수는 더 적은 비트로 표현 가능
- 부동소수점 레퍼런스 응답과 비교해 오차 허용 범위 내 확인

### 4. HDL 구현 최적화
- 대칭 계수 → 프리애더로 승산기 절반 감소
- CSD(`CoeffMultipliers='csd'`) → 승산기 시프트-덧셈 대체 (완전병렬 한정)
- 파이프라이닝으로 클록 주파수 확보

## HDL 코드 생성
```matlab
% 설계된 모델에서 DUT 지정 후
makehdl('모델명/Filter_DUT');

% 또는 HDL Workflow Advisor로 고정소수점 변환 + HDL 생성 한번에
hdlexport('myfilter');
```

## 검증 포인트
- 생성 HDL 시뮬레이션 결과 ↔ 부동소수점 레퍼런스 응답 비교
- 주파수 응답(저지대역 감쇠)이 사양을 만족하는지
- 자원(승산기·LUT·비트 폭) 리포트로 최적화 효과 확인

## 실행 요약
1. `fir1` 등으로 FIR 계수 설계
2. Fixed-Point Designer로 고정소수점 타입 결정
3. 응답 특성(저지대역 감쇠)에 맞춰 비트 폭 최소화
4. 대칭·CSD 등 HDL 최적화 적용
5. HDL 생성 → 응답·자원 검증

## 참고
- 공식 문서: DSP HDL Toolbox → Optimized Fixed-Point FIR Filters (Open Live Script)
- 연관: FIR Filter Design, Discrete FIR Filter, HDL Filter Architectures
