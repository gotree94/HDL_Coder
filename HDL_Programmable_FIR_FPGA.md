# HDL Coder 예제: Programmable FIR Filter for FPGA

> 공식 예제: **"Programmable FIR Filter for FPGA"**
> 목적: FPGA에서 실행 중에 **필터 계수를 변경**할 수 있는 프로그래머블 FIR 필터를 구현. **메모리-스타일 인터페이스**로 계수를 로드.

## 개요
하드웨어 동작 중에도 필터 응답을 바꿀 수 있는 **프로그래머블 FIR 필터**를 구현합니다. 필터 계수를 내부 레지스터에 **메모리-스타일 인터페이스(Memory-Style Interface)** 로 로드하여, 동일한 필터 하드웨어로 여러 다른 응답(저역/고역 등)을 구현할 수 있습니다.

## 필요한 툴박스
- DSP HDL Toolbox
- HDL Coder
- Simulink

## 핵심 개념: 프로그래머블 FIR
일반 FIR은 계수를 ROM/파라미터에 고정. 프로그래머블 FIR은 **계수를 런타임에 기록(write)** 하여 응답 변경.

### 응용
- 소프트웨어 정의 라디오(SDR) — 주파수 대역별 필터 재설정
- 적응형 통신 — 채널 상태에 따라 필터 재구성
- 테스트/측정 장비 — 다양한 필터 사양 시험

## 모델 구성
이 예제는 **2개의 필터 뱅크**(저역통과 + 고역통과)를 구현하며, 계수를 메모리 인터페이스로 쓸 때마다 응답이 바뀝니다.

### 주요 구성 요소
| 구성 | 역할 |
|------|------|
| **Programmable FIR (Memory Interface) 서브시스템** | `Discrete FIR Filter` 블록, `Coefficients source = Input port (Memory interface)` |
| **Host Behavioral Model 서브시스템** | 호스트가 계수를 메모리 스타일 포트로 쓰는 동작 모델 |
| 출력 `valid` 신호 | 출력 데이터가 유효한 시점 표시 |

### 계수 로딩 동작
1. **Host**가 저역통과 계수를 메모리 인터페이스로 필터에 기록
2. 입력 chirp 샘플을 계수 기록 완료까지 지연
3. 이후 고역통과 계수 로드
4. 계수를 쓰는 동안 필터는 입력 데이터 무시
5. `output valid` 신호로 출력 유효 시점 판별

### 계수 설정 (Model Properties > Callbacks > InitFcn)
```matlab
% 저역통과 / 고역통과 계수 정의
lowpass_coeff  = ...;   % 저역 응답 계수
highpass_coeff = ...;   % 고역 응답 계수
% 두 계수는 동일한 길이·대칭 구조 (승산기 공유 최적화 조건)
```

## 대칭 구조를 통한 승산기 공유
두 계수 집합이 **같은 길이와 대칭 구조**를 가지도록 지정하면:
- 동일한 하드웨어(승산기)를 두 응답에 공유
- 대칭 계수는 프리애더(덧셈 후 곱셈)로 승산기 절반 감소
- `Coefficient prototype` 파라미터로 계수의 대칭성·영점 위치를 지정해 자원 최적화

## HDL 코드 생성
```matlab
open_system(모델명);
systemname = [모델명 '/Programmable FIR(Memory Interface)'];
makehdl(systemname);   % HDL 생성
```

### 테스트벤치
```matlab
% HDL 시뮬레이션 결과와 Simulink 동작 비교용 테스트벤치 생성
makehdltb(systemname);
```

## 메모리 인터페이스 포트
`Coefficients source = Input port (Memory interface)` 설정 시 추가 포트:
- `coeff_data` — 계수 데이터
- `coeff_address` — 계수 주소
- `coeff_write_enable` — 계수 쓰기 인에이블
- `coeff_reset` — 계수 리셋

이 포트로 외부(호스트)가 필터 계수를 순차 기록.

## 실행 요약
1. `Discrete FIR Filter` 블록, `Coefficients source = Input port (Memory interface)` 설정
2. 두 계수(저역/고역)를 동일 대칭 구조로 정의
3. Host Behavioral Model로 계수 로딩 동작 모델링
4. `makehdl`로 HDL 생성 + 테스트벤치 검증

## 참고
- 공식 문서: https://www.mathworks.com/help/dsp/ug/generate-hdl-code-for-programmable-fir-filter.html
- 연관: Optimize Programmable FIR Filter Resources, FIR Filter Architectures for FPGAs and ASICs
