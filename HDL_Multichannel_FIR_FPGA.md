# HDL Coder 예제: Multichannel FIR Filter for FPGA

> 공식 예제: **"Multichannel FIR Filter for FPGA"**
> 목적: **다중 입력 데이터 스트림(채널)** 을 처리하는 FIR 필터를 구현하고, 하드웨어 **면적 효율(area efficiency)** 을 높이는 방법.

## 개요
여러 개의 입력 스트림(채널)을 받는 **다중 채널 FIR 필터**를 FPGA용으로 구현합니다. 초기 다중 채널 구현 후, `ChannelSharing` 설정을 적용해 **하나의 필터 하드웨어를 여러 채널이 공유**하도록 하여 하드웨어 면적을 줄이는 것이 핵심입니다.

## 필요한 툴박스
- DSP System Toolbox
- DSP HDL Toolbox
- HDL Coder
- Simulink

## 다중 채널 FIR의 필요성
- **MIMO 통신, 다중 안테나, 배열 신호처리** 등에서 여러 채널을 동시 처리
- LTE/5G 다중 안테나 시스템, 위상배열 레이더 등
- 각 채널은 동일 필터 특성 적용

## 핵심: ChannelSharing (채널 공유)
`Discrete FIR Filter` 블록의 **`ChannelSharing` HDL 구현 파라미터**:
| 값 | 동작 | 면적 |
|----|------|------|
| `off` (기본) | 채널마다 별도 필터 하드웨어 구현 | 큼 (채널 수만큼) |
| `on` | **단일 필터 구현을 채널들이 시분할 공유** | 크게 감소 |

```
ChannelSharing = on:
  채널 0 ─┐
  채널 1 ─┼──► [공유 필터 하드웨어] ──► 출력 MUX
  채널 2 ─┤
  채널 N ─┘   (필터를 클록 오버라이딩하여 채널별 순차 처리)
```
채널 공유 시 필터를 채널 수만큼 **오버클록(overclock)** 하여 각 채널을 순차 처리하므로, 승산기·파이프라인 자원을 여러 채널이 함께 사용해 면적을 크게 절감합니다.

## 구현 단계

### 1단계: 기본 다중 채널 FIR 구현
- `Discrete FIR Filter` 블록에 다중 채널 입력(예: 벡터/프레임 기반) 구성
- 각 채널에 동일 계수 적용

### 2단계: ChannelSharing으로 면적 최적화
```matlab
% Discrete FIR Filter 블록의 HDL Block Properties에서
% ChannelSharing = 'on' 설정 후 HDL 생성
makehdl('모델명/DUT');
```

## HDL 코드 생성
```matlab
open_system(모델명);
makehdl('모델명/MultichannelFIR');
```

## 합성 결과 비교
`ChannelSharing on` 적용 전후 **Resource Utilization Report** 비교:
- **DSP 슬라이스(승산기)**: 채널 공유로 크게 감소
- **LUT/FF**: 유사하거나 감소
- **fMAX**: 오버클록으로 인해 다소 조정 필요할 수 있음

## 장단점 정리
| ChannelSharing off | ChannelSharing on |
|--------------------|-------------------|
| 채널당 필터 중복 → 면적 큼 | 단일 필터 공유 → 면적 작음 |
| 동시 처리, 낮은 지연 | 오버클록, 처리 지연 다소 증가 |
| 구현 단순 | 제어 로직 필요 |

## 실행 요약
1. `Discrete FIR Filter` 블록으로 다중 채널 FIR 모델링
2. 기본 하드웨어 구현으로 HDL 생성·검증
3. `ChannelSharing = 'on'` 적용해 **면적 최적화**
4. 합성 전후 자원 사용량 비교로 절감 효과 확인

## 참고
- 공식 문서: DSP HDL Toolbox → Multichannel FIR Filter for FPGA (Open Script)
- 연관: ChannelSharing, FIR Filter Architectures for FPGAs and ASICs, DSP HDL IP Designer
