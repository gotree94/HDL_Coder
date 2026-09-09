# HDL Coder 예제: Fully Parallel Systolic FIR Filter

> 공식 예제: **"Fully Parallel Systolic FIR Filter Implementation"**
> 목적: **25-tap 저역통과 FIR 필터**를 완전병렬(Fully Parallel) 시스톨릭(systolic) 구조로 구현.

## 개요
25개의 탭(tap)을 가진 저역통과 FIR 필터를 **완전병렬 아키텍처**로 구현합니다. 모든 탭의 승산을 동시에 수행하므로 **최고 처리량(throughput)** 을 제공하지만 FPGA 로직 자원은 많이 사용합니다. DSP HDL Toolbox의 `Discrete FIR Filter` 블록을 사용합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- HDL Coder
- Fixed-Point Designer
- Simulink

## FIR 필터의 구조적 선택 (속도 vs 면적)
`Discrete FIR Filter` 블록은 `Filter structure` 파라미터로 여러 하드웨어 아키텍처를 지원합니다:
| 구조 | 승산기 수 | 처리량 | 면적 | 설명 |
|------|-----------|--------|------|------|
| **Fully Parallel** (이 예제) | 탭 수 N | 최고(GSPS) | 큼 | 모든 계수 승산을 병렬로, 시스톨릭 파이프라인 |
| Partly Serial | N/F | 중간 | 중간 | 승산기 일부 공유 |
| Fully Serial | 1 | 낮음 | 최소 | 승산기 1개 순차 사용 |

**완전병렬 시스톨릭 구조**:
- 각 탭마다 승산기 + 파이프라인 레지스터 배치
- 데이터가 레지스터 체인을 따라 "박자(systolic)"에 맞춰 흐름
- 선형 위상의 대칭 계수 활용 시 승산기 수는 N/2까지 감소 가능

## 시스톨릭(Systolic) 아키텍처 특징
```
x[n] ──►[R]──►[×b0]──►[+]──►[R]──►[×b1]──►[+]──► ...
             │                  │
            [R]                [R]
```
- **파이프라인 레지스터(R)** 를 승산기 사이에 배치해 클록 주파수 향상
- 데이터가 일정한 속도로 흐르는 스트리밍 처리에 최적
- **GSPS(gigasamples-per-second)** 초고속 데이터 처리 가능
- FPGA DSP 블록에 승산기·파이프라인·프리애더가 잘 매핑되도록 벤더 특정 하드웨어 고려

## 모델 구성
- `Discrete FIR Filter` 블록에 25-tap 저역통과 계수 지정
- 입력/출력은 스트리밍 인터페이스 + `valid`/`reset` 하드웨어 제어 신호
- 고정소수점 데이터 타입 사용

## HDL 코드 생성
```matlab
% DUT 서브시스템 지정 후 스크립트로 생성
open_system('...');   % 예제 모델 로드

% HDL 생성
makehdl('모델명/DUT');   % Verilog 또는 VHDL 생성
```

### 생성 관련 설정
- **Filter structure**: `Fully parallel systolic` 선택
- **Coefficient source**: 계수는 블록 파라미터(ROM) 또는 입력 포트
- **Coefficient multiplier**: `multipliers`(기본, 승산기 유지) 또는 `csd`(시프트-덧셈으로 대체, 완전병렬에서만 지원)
- **DSP block 매핑**: 목표 FPGA 벤더 지정 시 DSP 슬라이스에 맞게 파이프라인 최적화

## 합성 결과 (성능/자원)
- **fMAX**: 시스톨릭 파이프라이닝으로 높은 클록 주파수
- **자원**: 탭 수에 비례한 높은 LUT/FF/DSP 사용
- 벤치마크: 예를 들어 Xilinx Virtex-6 등에서 합성 결과를 리포트로 확인

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 최고 처리량 (GSPS) | FPGA 로직 자원 다량 사용 |
| 높은 클록 주파수 (시스톨릭) | 승산기 수 많음 |
| 스트리밍/초고속 응용 적합 | 전력 소모 큼 |

## 실행 요약
1. `Discrete FIR Filter` 블록으로 25-tap 저역통과 FIR 설계
2. `Filter structure` = **Fully parallel systolic**
3. 고정소수점 타입 지정
4. `makehdl`로 HDL 생성
5. FPGA 합성으로 자원·속도 확인

## 참고
- 공식 문서: DSP HDL Toolbox → Fully Parallel Systolic FIR Filter Implementation (Open Model)
- 연관: Partly Serial Systolic FIR, FIR Filter Architectures for FPGAs and ASICs
