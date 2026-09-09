# HDL Coder 예제: Implement FFT Algorithm for FPGA

> 공식 예제: **"Implement FFT Algorithm for FPGA"**
> 목적: DSP HDL Toolbox의 **FFT 블록**이 제공하는 두 가지 하드웨어 아키텍처(**Streaming Radix 2²**과 **Burst Radix 2**)를 Simulink에서 구현하고 HDL 코드를 생성.

## 개요
FPGA를 겨냥한 하드웨어 FFT를 DSP HDL Toolbox 블록으로 구현하는 예제입니다. DSP System Toolbox의 프레임 기반·부동소수점 함수는 하드웨어 설계의 **동작 레퍼런스(behavioral reference)** 로 사용하고, 실제 하드웨어 구현은 **스트리밍 데이터 인터페이스 + 고정소수점**을 사용합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- HDL Coder
- Simulink
- (레퍼런스) DSP System Toolbox

## 하드웨어 FFT의 핵심 개념
효율적인 하드웨어 설계는:
- **스트리밍 데이터 인터페이스** 사용
- **고정소수점(fixed-point)** 데이터 타입
- **하드웨어 제어 신호**: `valid`, `reset`, `backpressure`
- **벡터 입력 + 병렬 연산**으로 GSPS(초당 기가샘플) 초고속 달성

DSP HDL Toolbox 블록은 이런 하드웨어 최적화 아키텍처(스트리밍 인터페이스, 하드웨어 지연, 제어 신호)를 Simulink에서 그대로 모델링합니다.

## FFT 블록의 두 가지 아키텍처
`FFT` 블록의 `Architecture` 파라미터로 선택:

| 아키텍처 | 용도 | 특징 |
|---------|------|------|
| **Streaming Radix 2²** | **고처리량(고속)** 응용 | 벡터 입력으로 **GSPS/초고샘플링률** 달성. 파이프라인 스트리밍 |
| **Burst Radix 2** | **저면적** 응용 | **복소 butterfly 1개만** 사용, 연속 버스트 처리, 면적 최소 |

- `Streaming`: 데이터가 연속 흐름 → 최고 처리량
- `Burst`: FFT 데이터를 버스트로 처리(입력→계산→출력 반복) → 승산기·자원 절감

## 모델 구성
이 예제는 **두 가지 모델**을 포함:
1. **Streaming 모델**: 입력/출력 `valid` 제어 신호로 데이터율을 클록율과 독립적으로 모델링
2. **Burst 모델**: Burst Radix 2 아키텍처 시연

FFT 블록 설정:
- FFT 길이: 2의 거듭제곱 (8 ~ 65536, R2022a부터 길이 4 지원)
- 실수/복소 입력 지원
- 고정소수점(DSP HDL Toolbox 기본) 또는 부동소수점(native floating point)

## HDL 코드 생성
```matlab
% FFT DUT 서브시스템 지정 (Streaming 또는 Burst)
open_system('FFTHDLOptimizedExample_Streaming');  % 스트리밍 모델 예

% 버스트 모델의 FFT DUT에 대한 HDL + 테스트벤치 생성
systemname = 'FFTHDLOptimizedExample_Burst/FFT Burst';
makehdl(systemname);
makehdltb(systemname);
```

### 성능/자원 리포트
생성 HDL을 FPGA(Xilinx Virtex-6 등)에 합성한 결과 리포트 확인:
- LUT, FF, DSP 슬라이스, BRAM 사용량
- fMAX(최대 클록 주파수)
- 아키텍처별(streaming vs burst) 자원·성능 비교

## DSP HDL IP Designer (대안)
Simulink 모델을 만들지 않고도 **DSP HDL IP Designer 앱**을 이용해 FFT 알고리즘에서 직접 HDL 코드를 생성할 수 있습니다.

## 장단점 정리
| Streaming Radix 2² | Burst Radix 2 |
|--------------------|---------------|
| 초고속(GSPS), 연속 처리 | 낮은 면적 |
| 자원 사용 많음 | 처리량 낮음(버스트) |
| 고속 통신·레이더 | 저전력·소형 응용 |

## 실행 요약
1. FFT 블록과 아키텍처(Streaming/Burst) 선택
2. valid/reset 제어 신호로 스트리밍 모델링
3. 고정소수점 타입 설정
4. `makehdl`로 HDL + 테스트벤치 생성
5. FPGA 합성으로 성능·자원 확인

## 참고
- 공식 문서: https://www.mathworks.com/help/dsphdl/gs/generate-hdl-code-for-fft-hdl-optimized-block.html
- 연관: Frequency-Domain Filtering in HDL, FFT/IFFT 블록 문서
