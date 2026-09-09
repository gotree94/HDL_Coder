# HDL Coder 예제: Real-Time Simulation of Modular Multilevel Converter (MMC) on FPGA

> 공식 예제: **"Real-Time Simulation of Modular Multilevel Converter on FPGA"**
> 목적: 전력전자 응용인 **모듈형 멀티레벨 컨버터(MMC)** 를 FPGA에서 **실시간 시뮬레이션**하고 HDL 코드 생성.

## 개요
전력전자의 대표 장치인 **MMC(Modular Multilevel Converter)** 를 FPGA에서 **실시간(real-time) 시뮬레이션**하기 위해 HDL을 구현합니다. 대규모 전력 시스템에서 컨버터의 수치 모델을 FPGA로 고속 병렬 계산하는 전력전자-하드웨어 융합 예제입니다.

## 필요한 툴박스
- Simscape Electrical (MMC 모델링)
- HDL Coder
- Simulink
- Fixed-Point Designer
- (선택) Speedgoat 등 실시간 타깃 / FPGA 보드

## MMC(Modular Multilevel Converter)란?
- 고전압 DC(HVDC) 송전, 전력 품질 개선, 모터 구동 등에 사용되는 최신 멀티레벨 컨버터
- 수십~수백 개의 **서브모듈(서브모듈) 셀**을 직렬 연결해 고품질 다단 파형 생성
- 수천 개의 스위칭 소자 → **실시간 시뮬레이션이 매우 어려움**(계산량 폭증)
- 소프트웨어 시뮬레이션으로는 수백 마이크로초~밀리초가 걸려 실시간 불가

## FPGA 실시간 시뮬레이션 필요성
- **하드웨어 인더루프(HIL) 테스트**: 실제 컨트롤러를 FPGA로 시뮬레이션한 마이크로그리드/컨버터에 연결
- FPGA 병렬 처리로 **마이크로초 미만 스텝** 실시간 계산 가능
- 소프트웨어(CPU) 대비 훨씬 짧은 시뮬레이션 스텝 → 더 정확한 전력전자 모델링
- HVDC, 마이크로그리드, 전기차 충전 등 전력 변환 검증

## HDL 구현 접근
MMC의 계산 집약적 부분(서브모듈 상태·전류 계산)을 병렬 하드웨어로 구현:
```
MMC 전력회로 모델 (수천 셀)
        │
        ▼  (HDL Coder로 병렬 하드웨어 계산)
[FPGA 실시간 시뮬레이터] ──► I/O로 컨트롤러와 연결
```

### 구현 고려사항
- **고정소수점** 수치 모델 — 실시간 계산을 위해
- **병렬화**: 여러 서브모듈을 동시 계산
- **파이프라이닝**으로 계산 스텝 최소화
- 고정 시간 스텝의 이산화(전력회로 ODE)

### 실시간 계산 파이프라인
1. 스위칭 상태 수신(컨트롤러 입력)
2. 서브모듈 캐패시터 전압·전류 병렬 계산
3. 출력 전압/전류 계산 → I/O로 출력
4. 마이크로초 이내 완료 → 실시간 반복

## HDL 코드 생성
```matlab
% Simscape 전력회로 → HDL 호환 이산 모델로 변환
% 실시간 타깃 구성(Digital Twin / FPGA)
makehdl('MMC_Model/DUT');
```

## 검증
- 소프트웨어(Simscape) 오프라인 시뮬레이션과 FPGA 계산 결과 비교
- 다양한 스위칭 시나리오에서 전압·전류 파형 일치 확인
- 실시간 스텝 시간(마이크로초) 달성 여부 측정
- 스텝 크기·고정소수점에 따른 정확도 분석

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 마이크로초 단위 실시간 시뮬레이션 | 구현·설정 복잡 |
| HIL 검증 가능 | 고정소수점 정확도 관리 필요 |
| 대규모 전력 시스템 병렬 처리 | FPGA 자원 요구 큼 |

## 실행 요약
1. Simscape Electrical로 MMC 모델 구성
2. HDL 호환 이산·고정소수점 변환
3. FPGA 실시간 타깃 설정, 병렬 계산 구현
4. 오프라인 결과와 비교 검증 → HDL 생성

## 참고
- 공식 문서: HDL Coder / Simscape → Real-Time Simulation of Modular Multilevel Converter on FPGA (Overview 영상 포함)
- 연관: Simscape Electrical, FPGA 기본 실시간 배포, 전력전자 HDL
