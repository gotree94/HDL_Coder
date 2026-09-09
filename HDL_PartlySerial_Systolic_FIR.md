# HDL Coder 예제: Partly Serial Systolic FIR Filter

> 공식 예제: **"Partly Serial Systolic FIR Filter Implementation"**
> 목적: **32-tap 저역통과 FIR 필터**를 부분직렬(Partly Serial) 시스톨릭 구조로 구현하여 **승산기 자원을 공유**.

## 개요
32개의 탭을 가진 저역통과 FIR 필터를 **부분직렬(Partly Serial) 시스톨릭** 구조로 구현합니다. 완전병렬 구현과 달리 승산기를 **여러 탭이 시간 분할(시분할)로 공유**하므로 FPGA의 승산기(DSP 슬라이스) 개수를 크게 줄일 수 있습니다. 처리량과 면적의 중간 균형을 제공합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- HDL Coder
- Fixed-Point Designer
- Simulink

## 부분직렬(Partly Serial) 구조 개념
```
[탭 묶음] ──► [공유 승산기]          [탭 묶음 2] ──► [공유 승산기]
                    │                              │
                 [덧셈 누적]                   [덧셈 누적]
```
- **승산기 공유**: F개의 탭이 1개의 승산기를 시분할 사용 → 승산기 수 N/F로 감소
- `F` = **Serialization Factor**(직렬화 계수)
- 승산기는 입력 데이터 클록보다 빠른 속도로 오버클록되어 여러 탭 처리
- 완전병렬(N개 승산기)과 완전직렬(1개 승산기)의 중간 단계

### 구조 비교 표
| 구조 | 승산기 수 | 처리량 | 면적 |
|------|-----------|--------|------|
| Fully Parallel | N (32) | 최고 | 최대 |
| **Partly Serial** (이 예제) | N/F | 중간 | 중간 |
| Fully Serial | 1 | 최저 | 최소 |

## 시스톨릭(Systolic) + 직렬 결합
- 시스톨릭 파이프라인 레지스터로 클록 주파수 유지
- 부분직렬로 승산기 수 감소
- **대칭 계수** 활용 시 프리애더(더한 뒤 곱셈)로 승산기 추가 절감
- 벤더 FPGA의 DSP 블록 구조에 승산기·파이프라인·프리애더를 맞춰 매핑

## 모델 구성
- `Discrete FIR Filter` 블록에 32-tap 저역통과 계수 지정
- `Filter structure` = **Partly serial systolic** 선택
- 고정소수점 데이터 타입

## HDL 코드 생성
```matlab
% DUT 서브시스템 지정
makehdl('모델명/DUT');   % VHDL/Verilog 생성
```

### 생성 관련 설정
- **Serialization factor (F)**: 승산기 공유 정도 조절 (예: F=8 → 승산기 4개)
- **Coefficient multiplier**: `multipliers`(기본) — 부분직렬은 CSD 미지원(완전병렬만 지원)
- 파이프라인 레지스터로 클록 속도 확보

## 합성 결과 (성능/자원)
- **승산기(DSP)**: 완전병렬 대비 크게 감소
- **fMAX**: 시스톨릭 파이프라이닝으로 높은 수준 유지
- **LUT/FF**: 중간 수준

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 승산기 자원 절감 (완전병렬 대비) | 처리량이 완전병렬보다 낮음 |
| 높은 클록 주파수 유지 | 직렬화 계수에 따라 지연(latency) 증가 |
| 면적·속도 균형 | 제어 로직 추가 |

## 실행 요약
1. `Discrete FIR Filter` 블록으로 32-tap 저역통과 FIR 설계
2. `Filter structure` = **Partly serial systolic**
3. `Serialization factor`로 승산기 공유 정도 설정
4. `makehdl`로 HDL 생성 → FPGA 합성으로 자원·속도 확인

## 참고
- 공식 문서: DSP HDL Toolbox → Partly Serial Systolic FIR Filter Implementation (Open Model)
- 연관: Fully Parallel Systolic FIR, FIR Filter Architectures for FPGAs and ASICs
