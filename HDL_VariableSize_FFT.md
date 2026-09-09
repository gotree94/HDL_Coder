# HDL Coder 예제: HDL Implementation of Variable-Size FFT

> 공식 예제: **"HDL Implementation of Variable-Size FFT"**
> 목적: **단일 FFT 코어**를 사용하여 **가변 크기 FFT**를 구현. (Wireless HDL Toolbox)

## 개요
런타임에 FFT 크기(포인트 수)를 변경할 수 있는 **가변 크기 FFT(Variable-Size FFT)** 를 하나의 하드웨어 FFT 코어로 구현합니다. 고정 크기 FFT 여러 개를 배치하는 대신 단일 코어를 재구성하므로 **하드웨어 자원을 크게 절약**할 수 있습니다.

## 필요한 툴박스
- Wireless HDL Toolbox
- (또는 DSP HDL Toolbox의 FFT 블록 기반)
- HDL Coder
- Simulink

## 가변 크기 FFT의 필요성
- **LTE/5G OFDM**: 시스템 대역폭·리소스 블록 수에 따라 FFT 크기(128/256/512/1024/2048 등)가 달라짐
- **다중 표준 라디오(SDR)**: 802.11(64), LTE(128~2048), 5G NR(256~4096) 등 서로 다른 FFT 크기 지원
- 각 크기마다 FFT 코어를 별도로 두면 자원이 낭비 → **가변 크기 단일 코어**로 해결

## 구현 방식
단일 FFT 코어가 다양한 FFT 크기를 처리하는 **가변 크기 아키텍처**:
```
FFT 크기 설정 신호(VariableSize)
        │
        ▼
[단일 FFT 코어] ──► 크기에 맞는 출력
  - 크기별 트위들(twiddle) 계수
  - 크기별 파이프라인/제어
```

### 핵심 요소
- **FFT 크기 제어**: 런타임에 FFT 포인트 수를 지정하는 신호/레지스터
- **가변 길이 유효 데이터**: 출력 데이터의 유효 크기가 FFT 크기에 따라 변동
- **트위들 팩터 관리**: 서로 다른 FFT 크기에 대응하는 twiddle 계수 저장
- **제어 신호**: 크기 변경 시 파이프라인 리셋·재구성

### 하드웨어 절약
- 고정 FFT N개 대신 **가변 FFT 1개** → 승산기·BRAM·로직 대폭 감소
- 활성화된 크기만 연산하므로 전력 효율 우수

## 모델 구성
- DSP HDL Toolbox / Wireless HDL Toolbox의 `FFT` 블록 기반
- FFT 크기 설정 입력 포트 추가
- 스트리밍 인터페이스 + `valid`/`reset` 제어 신호

## HDL 코드 생성
```matlab
makehdl('모델명/VariableFFT_DUT');
makehdltb('모델명/VariableFFT_DUT');   % 여러 FFT 크기로 검증
```

## 검증
- 여러 FFT 크기(예: 64/128/256)로 전환하며 참조 FFT와 출력 비교
- 크기 전환 시 올바른 출력 유효 신호 확인
- 각 크기에서의 주파수 응답 정확성 검증

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 단일 코어로 여러 FFT 크기 지원 | 크기 전환 시 제어 로직 복잡 |
| 하드웨어 자원·전력 절감 | 크기별 최적 성능보다 다소 저하 |
| 다중 표준(OFDM/SDR) 유연성 | 재구성 시간 필요 |

## 실행 요약
1. 가변 크기 FFT 코어 모델 구성 (FFT 크기 제어 포함)
2. 여러 FFT 크기로 참조 출력과 비교 검증
3. `makehdl`로 HDL 생성
4. 하드웨어 자원 절감 효과 확인

## 참고
- 공식 문서: Wireless HDL Toolbox → HDL Implementation of Variable-Size FFT
- 연관: Implement FFT Algorithm for FPGA, Channelizer/OFDM 설계
