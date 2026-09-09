# HDL Coder 예제: LED Blink IP Core Generation (Zynq ZedBoard)

> 공식 예제: **"Get Started with IP Core Generation from MATLAB Function"**
> 목적: MATLAB 알고리즘에서 **커스텀 IP 코어**를 생성해 FPGA 보드(Zynq ZedBoard)의 **LED를 깜빡이는** 하드웨어에 배포.

## 개요
간단한 MATLAB 알고리즘(설계)을 HDL Coder의 **HDL Workflow Advisor**로 **IP 코어**로 변환하고, Xilinx Zynq ZedBoard FPGA에서 실행해 **LED를 깜빡이게** 하는 가장 대표적인 입문 하드웨어 배포 예제입니다.

## 필요한 툴박스
- HDL Coder
- HDL Coder Support Package for Xilinx FPGA and SoC Devices
- Fixed-Point Designer
- 타사: Xilinx Vivado, Xilinx Zynq ZedBoard(Zynq-7000)

## 목표
- MATLAB 함수를 IP 코어로 변환
- Zynq SoC(ARM 프로세서 + FPGA 패브릭)에 배포
- FPGA에서 LED 깜빡임 구현 (하드웨어-소프트웨어 협력 설계 예시)

## 워크플로 (HDL Workflow Advisor)

### 1단계: MATLAB 설계/알고리즘 준비
LED를 깜빡이는 카운터 기반 설계 예시:
```matlab
function led = blink_led(enable)
% 1초 주기로 LED ON/OFF 제어
persistent counter;
if isempty(counter), counter = 0; end
counter = counter + 1;
if enable && counter >= 50000000   % 50MHz 클록 기준 ~1초
    led = ~...;  % LED 토글
    counter = 0;
end
end
```

### 2단계: HDL Workflow Advisor 설정
```matlab
% 보드 설정
hwboard = 'Xilinx Zynq-7000 ZC702 Evaluation Kit';  % 또는 ZedBoard
hdlexample('ex_blink');
```

HDL Workflow Advisor 단계:
1. **Set Target Device** — Zynq 보드 선택
2. **Set Target Reference Design** — 보드 IP 통합(IPI) 설정, AXI 레지스터
3. **Fixed-Point Conversion** — 고정소수점 변환
4. **HDL Code Generation** — RTL 생성
5. **Generate IP Core** — **커스텀 IP 코어**로 패키징 (AXI 인터페이스 포함)
6. **Vivado Project / Synthesis** — Vivado에서 합성·구현
7. **Deploy to Target** — ZedBoard 배포, LED 확인

### IP 코어 생성
- **AXI4-Lite 슬레이브 인터페이스**로 ARM 프로세서가 FPGA IP 제어
- HDL Workflow Advisor가 보드 지원 패키지 기반으로 IP 통합 자동화

## 하드웨어-소프트웨어 협력 설계 (SoC)
Zynq SoC 아키텍처:
```
┌────────────── Zynq SoC ──────────────┐
│  ARM Cortex-A9 (PS)  ←── AXI ──►  FPGA (PL)  │
│  (소프트웨어 제어)            (커스텀 IP: LED 제어)   │
└─────────────────────────────────────────┘
```
- **PS(Processing System)**: ARM 프로세서 — AXI로 IP 제어
- **PL(Programmable Logic)**: FPGA 패브릭 — 생성된 IP 코어 동작
- LED GPIO는 PL의 IP가 제어

## 결과 확인
- ZedBoard에서 생성 IP가 LED를 주기적으로 깜빡이게 함
- AXI 레지스터로 enable·속도 제어
- Vivado 하드웨어 연동으로 비트스트림 프로그래밍

## 실행 요약
1. LED 제어 MATLAB 함수 작성
2. HDL Workflow Advisor로 IP 코어 생성 (AXI 인터페이스)
3. ZedBoard 대상 배포 설정
4. Vivado 합성·비트스트림 → 보드에서 LED 확인

## 참고
- 공식 문서: https://www.mathworks.com/help/hdlcoder/getting-started-with-hdl-coder.html
- 연관: IP Core Generation from Simulink Model, Prototype Generated IP Core on Hardware Using FPGA I/O
