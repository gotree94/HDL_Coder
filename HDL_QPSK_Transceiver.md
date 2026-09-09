# HDL Coder 예제: QPSK Transmitter and Receiver

> 공식 예제: **"QPSK Transmitter and Receiver"**
> 목적: 하드웨어용 **QPSK 송신기/수신기**를 설계·검증하고 HDL 코드 생성. (Wireless HDL Toolbox)

## 개요
QPSK(Quadrature Phase Shift Keying) 변조 방식을 사용하는 **무선 송수신기**를 하드웨어 구현용으로 설계하고 검증합니다. Wireless HDL Toolbox를 사용해 송신기와 수신기를 모델링하고 HDL 코드를 생성합니다.

## 필요한 툴박스
- Wireless HDL Toolbox
- HDL Coder
- Simulink
- Communications Toolbox (레퍼런스/검증)

## QPSK 변조 기초
QPSK는 2비트를 하나의 심볼로 전송하는 위상 변조:
```
00 → +45°    01 → +135°    10 → −45°    11 → −135°
```
- I(동위상) / Q(직교) 2개 성분으로 4개 위상 상태 표현
- 위상 대역폭 효율이 BPSK의 2배

## QPSK 송신기 (Transmitter) 구조
```
비트 스트림 ──► [직렬↔병렬] ──► [성상 매핑(I/Q)] ──► [필터/파형 처리] ──► [I/Q 출력]
```
- 입력 비트를 I/Q 성분으로 매핑
- 펄스 성형 필터, 업샘플링 포함
- 하드웨어 제어 신호(`valid`, `reset`) 포함

## QPSK 수신기 (Receiver) 구조
```
I/Q 입력 ──► [동기화/등화] ──► [성상 판정(decision)] ──► [병렬↔직렬] ──► 비트 스트림
```
- 수신 신호 처리
- 성상도(Constellation) 판정으로 심볼 복조
- 하드웨어 파이프라인·제어 신호 포함

## HDL 코드 생성
```matlab
% Wireless HDL Toolbox 모델에서 DUT 서브시스템 지정
makehdl('QPSK_HDL/Tx');
makehdl('QPSK_HDL/Rx');
```

## 검증
- **HDL 프레임 기반 인터페이스**로 송수신 연결
- 송신기 출력과 수신기 입력 연결 시 **비트 오류율(BER)** 확인
- 성상도(다이어그램)로 복조 품질 시각화
- 스트리밍/프레임 처리 타이밍 검증
- HDL 시뮬레이션(Vivado/ModelSim)과 Simulink 결과 비교

## Wireless HDL Toolbox 특징
- 무선 통신 표준(OFDM 등) 하드웨어 구현용 블록
- 프레임 기반 데이터 처리
- 하드웨어 제어 신호(valid/start/end) 내장
- HDL Coder와 연동해 FPGA/ASIC RTL 생성

## 실행 요약
1. QPSK 송신기·수신기 모델 구성
2. HDL 프레임 인터페이스로 송수신 연결·검증
3. BER·성상도로 품질 확인
4. `makehdl`로 HDL 생성

## 참고
- 공식 문서: Wireless HDL Toolbox → QPSK Transmitter and Receiver (Open Live Script, R2026a 신규)
- 연관: HDL QAM Transmitter and Receiver, 무선 HDL 설계
