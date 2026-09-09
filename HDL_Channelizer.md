# HDL Coder 예제: High-Throughput Channelizer for FPGA

> 공식 예제: **"High-Throughput Channelizer for FPGA"**
> 목적: **폴리페이즈 필터뱅크(polyphase filter bank)** 로 고처리량 채널라이저를 구현 — **GSPS(초당 기가샘플)** 초고속 데이터 처리 (DSP HDL Toolbox Channelizer 블록).

## 개요
넓은 대역의 입력 신호를 **여러 개의 좁은 주파수 채널**로 분할하는 **채널라이저(Channelizer)** 를 FPGA용으로 구현합니다. DSP HDL Toolbox의 `Channelizer` 블록을 사용하며, 벡터 입력과 병렬 연산으로 **GSPS(초고샘플링률)** 데이터를 처리합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- HDL Coder
- Simulink

## Channelizer란?
주파수 분할 다중화(FDM) 수신기에서 넓은 대역을 다중 좁은 대역 채널로 분해:
- **용도**: 위성 통신, 케이블 모뎀, 5G/무선 백홀, 레이더
- 각 채널은 독립적으로 복조·처리 가능
- 구현: **폴리페이즈 필터뱅크 + FFT** 구조

```
Broadband 입력 ──► [폴리페이즈 필터뱅크] ──► [FFT] ──► 채널 0..N-1
                      (Channelizer 블록)
```

## GSPS 처리 (초고샘플링)
- 입력은 **JESD204B 인터페이스**로 벡터화되어 FPGA에서 낮은 클록으로 제공된다고 가정
- 알고리즘은 한 번에 **4개 샘플**을 처리
- 벡터 입력 + 병렬 연산으로 **초당 기가샘플(GSPS)** 처리 달성

## Channelizer 블록 특징
`Channelizer` 블록(DSP HDL Toolbox)은 자동으로:
- **데이터 타입 자동 선택**(고정소수점)
- **파이프라이닝 적용**
- **승산기 공유**(하드웨어 최적화)
- **계수 뱅크를 ROM**으로 구현
- FPGA DSP 블록에 맞는 파이프라인 스테이지 배치

### 첫 번째 모델 구성 (예)
- `Channelizer` 블록, **12-tap 필터** 설정 (좋은 스펙트럼 성능)
- **512-point FFT** + **12-tap 필터** 각 대역
- 벡터 크기 4 샘플
- `State Control (HDL Coder)` 블록으로 enable 로직 하드웨어 최적화 생성
```matlab
modelname = 'PolyphaseFilterBankHDLExample_HDLChannelizer';
```
- 파라미터(계수, 벡터 크기, FFT 길이)를 워크스페이스 변수로 쉽게 변경 가능

### 두 번째 모델 (수동 구현 시연)
- 폴리페이즈 필터뱅크 내부 구현을 보여주기 위해
- DSP HDL Toolbox FFT 블록으로 **512-point FFT** 구현
- 기본 Simulink 블록으로 **4-tap 필터** 각 대역 구성

## HDL 코드 생성
```matlab
open_system(modelname);
makehdl('모델명/Channelizer_HDL_DUT');
```

## 검증
- 입력 스펙트럼과 각 채널 출력의 주파수 분리 확인
- 채널 간 격리도(interference) 확인
- 참조 채널라이저와 출력 비교

## 장단점 정리
| 장점 | 단점 |
|------|------|
| GSPS 초고속 처리 | 자원 사용 (FFT + 필터뱅크) |
| 채널라이저 블록으로 빠른 설계 | 벡터/프레임 기반 처리 필요 |
| 자동 파이프라이닝·승산기 공유 | 다중 채널 제어 복잡 |

## 실행 요약
1. `Channelizer` 블록으로 폴리페이즈 필터뱅크 구성
2. FFT 길이·필터 탭·벡터 크기 설정
3. GSPS 스트리밍 처리 검증
4. `makehdl`로 HDL 생성

## 참고
- 공식 문서: https://www.mathworks.com/help/dsphdl/gs/generate-hdl-code-for-high-throughput-channelizer.html
- 연관: LCD DUC 설계, Implement FFT Algorithm for FPGA, Channelizer/Channel Synthesizer 블록
