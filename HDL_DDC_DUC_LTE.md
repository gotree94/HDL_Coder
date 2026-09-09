# HDL Coder 예제: LTE Digital Down/Up Converter (DDC/DUC) for FPGA

> 공식 예제: **"Implement Digital Downconverter / Upconverter for FPGA"** (LTE)
> 목적: **LTE용 디지털 하향 변환(Digital Downconverter, DDC)** 과 **상향 변환(Digital Upconverter, DUC)** 을 FPGA용으로 설계·구현.

## 개요
LTE(4G) 무선 통신 시스템에서 사용하는 **디지털 하향 변환기(DDC)** 와 **디지털 상향 변환기(DUC)** 를 FPGA용으로 구현합니다. 무선 송수신기의 핵심 신호 처리 블록으로, 중간 주파수(IF)와 기저대역(baseband) 사이의 주파수 변환을 디지털로 수행합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- Wireless HDL Toolbox
- HDL Coder
- Simulink

## DDC / DUC 개념
### Digital Downconverter (DDC) — 수신 경로
고주파(IF) 신호를 **기저대역(baseband) 저주파**로 내리는 변환기:
```
IF 신호 ──► [혼합기(NCO 국부발진)] ──► [저역통과 필터/데시메이션] ──► 기저대역
```
- **NCO(Numerically Controlled Oscillator)** 로 국부 발진 주파수 생성
- I/Q 복조(혼합)
- 데시메이션(샘플링 레이트 저감) FIR 필터
- 수신기에서 채널 선택·대역폭 축소

### Digital Upconverter (DUC) — 송신 경로
기저대역 신호를 **고주파(IF)로 올리는** 변환기:
```
기저대역 ──► [보간(interpolation) FIR 필터] ──► [혼합기(NCO)] ──► IF 신호
```
- 업샘플링(보간) 필터로 샘플링 레이트 증가
- NCO로 IF 반송파 변조
- 송신기에서 스펙트럼을 원하는 대역으로 이동

## 하드웨어 구성 요소
| 요소 | 역할 |
|------|------|
| **NCO** (DSP HDL Toolbox) | 정현파 발진(국부 발진주파수 생성) |
| **FIR Decimator / FIR Interpolator** | 데시메이션/보간 필터 |
| **Mixer(혼합기)** | 신호와 NCO 출력 곱셈 |
| 고정소수점 데이터 타입 | FPGA 하드웨어 최적화 |

### LTE 특성 반영
- LTE 채널 대역폭(1.4/3/5/10/15/20 MHz)에 맞는 필터 설계
- LTE 샘플링 레이트(예: 30.72 MSPS 등) 지원
- OFDM 기저대역 처리를 위한 정확한 I/Q 경로

## HDL 코드 생성
```matlab
makehdl('LTE_DDC/DUT');
makehdl('LTE_DUC/DUT');
```
- NCO, 필터 블록이 각각 하드웨어 친화적으로 합성
- 스트리밍 인터페이스 + `valid`/`reset` 제어 신호

## 검증
- 입력 테스트 톤의 주파수 이동(shift) 확인
- DDC 후 기저대역 신호의 스펙트럼 확인
- 주파수 응답·지연(Latency) 검증
- HDL 시뮬레이션과 Simulink 레퍼런스 비교

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 무선 기저대역 처리의 핵심 블록 | 필터·NCO 자원 사용 |
| 디지털 구현으로 유연한 주파수 제어 | 데시메이션/보간 지연 |
| DSP HDL 최적화 블록 재사용 | LTE 사양에 맞는 설계 필요 |

## 실행 요약
1. NCO + 데시메이션/보간 FIR로 DDC/DUC 구성
2. LTE 채널/샘플링 조건 설정
3. 주파수 이동·스펙트럼 검증
4. `makehdl`로 HDL 생성

## 참고
- 공식 문서: DSP HDL Toolbox → Implement Digital Downconverter / Digital Upconverter for FPGA
- 연관: NCO 블록, FIR Decimator/Interpolator, Channelizer
