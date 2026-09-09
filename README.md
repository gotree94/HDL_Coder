# MATLAB / Simulink HDL Coder 공식 예제 정리

> 바탕화면에 정리한 **MATLAB/Simulink HDL Coder** 공식 예제 상세 설명 모음.
> 각 예제는 별도 마크다운 파일로 정리되어 있습니다.

## 목차

### 1. 입문 / 기본 (Get Started)
| 파일 | 예제 | 요약 |
|------|------|------|
| [HDL_GetStarted_MATLAB_Counter.md](HDL_GetStarted_MATLAB_Counter.md) | MATLAB Counter → HDL | MATLAB 함수(카운터)의 HDL 생성 기초 튜토리얼 |
| [HDL_GetStarted_Simulink_Counter.md](HDL_GetStarted_Simulink_Counter.md) | Simulink Counter → HDL | Simulink 모델(카운터)의 HDL 생성 기초 |
| [HDL_Symmetric_FIR_WorkflowAdvisor.md](HDL_Symmetric_FIR_WorkflowAdvisor.md) | Symmetric FIR (Workflow Advisor) | HDL Workflow Advisor로 FIR 필터 생성·합성 |

### 2. DSP / 필터 (DSP HDL Toolbox)
| 파일 | 예제 | 요약 |
|------|------|------|
| [HDL_FullyParallel_Systolic_FIR.md](HDL_FullyParallel_Systolic_FIR.md) | Fully Parallel Systolic FIR | 25-tap 저역통과 FIR, 완전병렬 |
| [HDL_PartlySerial_Systolic_FIR.md](HDL_PartlySerial_Systolic_FIR.md) | Partly Serial Systolic FIR | 32-tap FIR, 승산기 자원 공유 |
| [HDL_Programmable_FIR_FPGA.md](HDL_Programmable_FIR_FPGA.md) | Programmable FIR for FPGA | 메모리 인터페이스로 계수 로딩 |
| [HDL_Multichannel_FIR_FPGA.md](HDL_Multichannel_FIR_FPGA.md) | Multichannel FIR for FPGA | 다중 입력 스트림 FIR, 면적 효율 |
| [HDL_Optimized_FixedPoint_FIR.md](HDL_Optimized_FixedPoint_FIR.md) | Optimized Fixed-Point FIR | 고정소수점 FIR 최적화 |
| [HDL_FrequencyDomain_Filtering.md](HDL_FrequencyDomain_Filtering.md) | Frequency-Domain Filtering | FFT/IFFT로 주파수영역 필터링 |
| [HDL_LMS_Filter.md](HDL_LMS_Filter.md) | HDL Implementation of LMS | LMS 적응형 필터(완전직렬) |

### 3. FFT / 변환
| 파일 | 예제 | 요약 |
|------|------|------|
| [HDL_Implement_FFT_FPGA.md](HDL_Implement_FFT_FPGA.md) | Implement FFT Algorithm for FPGA | FFT 2가지 하드웨어 아키텍처 |
| [HDL_VariableSize_FFT.md](HDL_VariableSize_FFT.md) | HDL Implementation of Variable-Size FFT | 가변 크기 FFT |
| [HDL_IFFT_FFT_Reuse.md](HDL_IFFT_FFT_Reuse.md) | FFT 기반 IFFT 구현 | FFT 블록 재사용으로 IFFT |
| [HDL_atan2_Function.md](HDL_atan2_Function.md) | Implement atan2 Function | atan2/CORDIC 하드웨어 구현 |
| [HDL_Channelizer.md](HDL_Channelizer.md) | High-Throughput Channelizer | polyphase 필터뱅크, GSPS |

### 4. 통신 (Wireless HDL / Communications)
| 파일 | 예제 | 요약 |
|------|------|------|
| [HDL_QPSK_Transceiver.md](HDL_QPSK_Transceiver.md) | QPSK Transmitter and Receiver | QPSK 송수신기 |
| [HDL_QAM_Transceiver.md](HDL_QAM_Transceiver.md) | HDL QAM Transmitter and Receiver | 64-QAM 송수신기 (R2026a) |
| [HDL_Modulation_Classification.md](HDL_Modulation_Classification.md) | Modulation Classification by FPGA | CNN을 Zynq에 배포 |
| [HDL_DDC_DUC_LTE.md](HDL_DDC_DUC_LTE.md) | LTE Digital Down/Up Converter | 고속 변환기, DDC/DUC |
| [HDL_ADS_B_Receiver.md](HDL_ADS_B_Receiver.md) | Airplane Tracking with ADS-B | ADS-B 수신기 |

### 5. 응용 / 기타
| 파일 | 예제 | 요약 |
|------|------|------|
| [HDL_CORDIC_atan2_Import.md](HDL_CORDIC_atan2_Import.md) | CORDIC atan2 → Simulink (`importhdl`) | Verilog 역생성 |
| [HDL_LED_Blink_IPCore.md](HDL_LED_Blink_IPCore.md) | LED Blink IP Core (Zynq) | IP 코어 생성·배포 |
| [HDL_Stateflow_Counter.md](HDL_Stateflow_Counter.md) | Generate HDL from Stateflow | Stateflow 카운터 → HDL |
| [HDL_MMC_RealTime.md](HDL_MMC_RealTime.md) | Real-Time MMC on FPGA | 전력전자 MMC 실시간 시뮬레이션 |
| [HDL_HLS_GetStarted.md](HDL_HLS_GetStarted.md) | MATLAB to HLS (HDL Coder App) | 고수준 합성 코드 생성 |

---

## 필요한 툴박스 (Toolbox)
| 툴박스 | 용도 |
|--------|------|
| **HDL Coder** | HDL(VHDL/Verilog/SystemVerilog) 코드 생성 (필수) |
| **Simulink** | 블록 기반 모델링 |
| **Fixed-Point Designer** | 고정소수점 데이터 타입 변환·최적화 |
| **DSP HDL Toolbox** | FPGA/ASIC용 하드웨어 최적화 DSP 블록 (FFT, FIR 등) |
| **DSP System Toolbox** | DSP 알고리즘의 동작 레퍼런스 |
| **Wireless HDL Toolbox** | 무선 통신 HDL 블록 (통신 예제용) |
| **Communications Toolbox** | 통신 시스템 모델링 |
| **HDL Verifier** | HDL 검증·테스트벤치 |

## HDL 코드 생성 기본 워크플로
```
MATLAB 함수 / Simulink 모델 / Stateflow 차트
        │  (HDL Coder / HDL Workflow Advisor)
        ▼
고정소수점 변환 (Fixed-Point Designer)
        ▼
HDL 코드 생성 (VHDL / Verilog / SystemVerilog)
        ▼
HDL 테스트벤치 생성·검증 (HDL Verifier)
        ▼
FPGA 합성·구현 (Vivado / Quartus / Libero) or ASIC 플로우
```

## 참고 링크
- 공식 예제 카탈로그: https://www.mathworks.com/help/hdlcoder/examples.html
- 공식 GitHub 저장소: https://github.com/mathworks/HDL-Coder
