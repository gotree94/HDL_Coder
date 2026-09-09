# HDL Coder 예제: Airplane Tracking with ADS-B Captured Data

> 공식 예제: **"Airplane Tracking with ADS-B Captured Data"**
> 목적: **ADS-B(Automatic Dependent Surveillance–Broadcast) 수신기**를 FPGA 구현용으로 설계하고, 실제 캡처 데이터로 항공기 추적 처리. (Wireless HDL Toolbox)

## 개요
항공기가 자동으로 위치·고도·속도 등을 방송하는 **ADS-B 신호(Automatic Dependent Surveillance–Broadcast)** 를 수신하고 처리하여 항공기를 추적하는 시스템을 FPGA용으로 구현합니다. 실제로 캡처된 ADS-B 데이터를 사용해 수신기 동작을 검증합니다.

## 필요한 툴박스
- Wireless HDL Toolbox
- HDL Coder
- Simulink
- (검증) 상용 ADS-B 수신기/데이터

## ADS-B란?
- 항공기가 주기적으로 **자신의 위치(GPS), 고도, 속도, 식별 코드**를 방송하는 1090 MHz 무선 신호
- 항공 교통 관제(ATC)에서 레이더 없이도 항공기를 추적
- 데이터 포맷: **1090ES / Mode-S Extended Squitter**, 펄스 위치 변조(PPM), 112비트 메시지

### ADS-B 메시지 구조 (일반)
```
[위치/고도/속도/식별 등] 112비트 프레임
- PPM(펄스 위치 변조) 인코딩
- CRC 오류 검출
```

## ADS-B 수신기 하드웨어 파이프라인
```
1080MHz RF ──► [전단(ADC)] ──► [검출·비트 동기화] ──► [PPM 복조] ──► [프레임/CRC 검사] ──► [메시지 해석] ──► 추적 화면
```
| 단계 | 역할 |
|------|------|
| 신호 검출/동기화 | 펄스 검출, 비트 클록 복원 |
| **PPM 복조** | 펄스 위치 변조 → 비트열 복원 |
| **프레임 동기화·CRC** | 112비트 프레임 유효성 검사 |
| **메시지 디코딩** | 위치/고도/속도/식별 추출 |
| **추적 표시** | 수신 항공기 그래픽 표시 |

## HDL 프레임 처리
- Wireless HDL Toolbox의 **프레임 기반 스트리밍 인터페이스**
- 하드웨어 제어 신호(valid/start/end)로 메시지 경계 처리
- 실시간(스트리밍)으로 다수의 항공기 ADS-B 메시지 처리

## HDL 코드 생성
```matlab
makehdl('ADS_B_Receiver/DUT');
```

## 검증
- **실제 캡처된 ADS-B 데이터**(파일)를 입력으로 재생
- 수신기가 항공기 식별 코드·위치·고도를 올바르게 디코딩하는지 확인
- 디코딩된 항공기를 지도/그래프에 추적 표시
- CRC 오류 통과율, 프레임 동기화 성능
- HDL 시뮬레이션과 MATLAB 레퍼런스 디코더 비교

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 실제 데이터로 현실적인 검증 | ADS-B 신호 처리 복잡(PPM, CRC) |
| 항공 교통 모니터링 응용 | 고속 RF 전단 필요 |
| 프레임 기반 실시간 처리 | 메시지 종류 다양 |

## 실행 요약
1. ADS-B 수신 파이프라인(검출·PPM 복조·프레임/CRC·디코딩) 구성
2. 캡처된 ADS-B 데이터 입력
3. 항공기 추적·디코딩 검증
4. `makehdl`로 HDL 생성

## 참고
- 공식 문서: Wireless HDL Toolbox → Airplane Tracking with ADS-B Captured Data (Open Model)
- 연관: HDL 프레임 처리, 무선 수신기 설계
