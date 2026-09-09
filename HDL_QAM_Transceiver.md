# HDL Coder 예제: HDL QAM Transmitter and Receiver

> 공식 예제: **"HDL QAM Transmitter and Receiver"** (R2026a 신규)
> 목적: **64-QAM 송신기/수신기**를 HDL 코드 생성과 하드웨어 구현용으로 설계·검증. (Wireless HDL Toolbox)

## 개요
**64-QAM(Quadrature Amplitude Modulation)** 방식을 사용하는 송신기와 수신기를 하드웨어 구현용으로 설계하고 HDL 코드를 생성합니다. QPSK보다 높은 스펙트럼 효율(6비트/심볼)을 제공하는 64-QAM의 하드웨어 구현에 초점을 맞춥니다.

## 필요한 툴박스
- Wireless HDL Toolbox
- HDL Coder
- Simulink
- Communications Toolbox (레퍼런스)

## QAM 변조 기초
QAM은 진폭과 위상을 동시에 변조해 더 많은 비트를 전송:
| 성좌(Constellation) | 비트/심볼 | 심볼 수 |
|---------------------|-----------|---------|
| QPSK | 2 | 4 |
| 16-QAM | 4 | 16 |
| **64-QAM** (이 예제) | **6** | **64** |

- 64-QAM 성상도: 8×8 격자, I/Q 각 8레벨
- 높은 스펙트럼 효율 → 무선 LAN(802.11), 케이블 모뎀(DVB-C), 5G 등에 주로 사용
- 대신 잡음에 취약 → 채널 손상에 따른 정확한 판정 필요

## 64-QAM 송신기 구조
```
비트 ──► [직렬↔병렬(6비트)] ──► [QAM 성상 매핑] ──► [펄스 성형/필터] ──► [I/Q]
```
- 6비트를 하나의 64-QAM 심볼로 매핑
- I/Q 각각 3비트(8레벨)로 분할
- 스트리밍 프레임 인터페이스

## 64-QAM 수신기 구조
```
I/Q ──► [AGC/정규화] ──► [성상 판정] ──► [병렬↔직렬] ──► 비트
```
- 수신 심볼을 가장 가까운 성상점에 매핑(슬라이스 판정)
- 하드웨어 파이프라인·제어 신호

## HDL 코드 생성
```matlab
makehdl('QAM_64_HDL/Tx');
makehdl('QAM_64_HDL/Rx');
```

## 검증
- 송신-수신 연결 시 **BER(비트 오류율)** 측정
- 성상도(constellation diagram)로 복조 품질 확인
- SNR에 따른 BER 성능
- HDL 시뮬레이션과 Simulink 레퍼런스 비교

## 주의점
- 64-QAM은 정확한 진폭 판정 필요 → **AGC(자동 이득 제어)/정규화** 중요
- 고정소수점 양자화가 성상 판정 오류에 영향 → 비트 폭 설계 중요
- 고차 QAM일수록 펄스 성형 필터와 등화 품질 중요

## 실행 요약
1. 64-QAM 송신기·수신기 모델 구성
2. BER·성상도로 성능 검증
3. `makehdl`로 HDL 생성 · FPGA 합성

## 참고
- 공식 문서: Wireless HDL Toolbox → HDL QAM Transmitter and Receiver (Open Live Script, R2026a)
- 연관: QPSK Transmitter and Receiver, 무선 HDL 설계
