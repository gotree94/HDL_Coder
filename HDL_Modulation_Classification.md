# HDL Coder 예제: Modulation Classification by Using FPGA

> 공식 예제: **"Modulation Classification by Using FPGA"** (since R2022b)
> 목적: 사전 학습된 **컨볼루션 신경망(CNN)** 을 FPGA(Xilinx Zynq UltraScale+ MPSoC ZCU102)에 배포하여 **변조 분류(modulation classification)** 실행.

## 개요
무선 신호의 **변조 방식을 자동으로 분류**하는 CNN 모델을 FPGA 하드웨어에 배포합니다. 소프트웨어 정의 라디오(SDR), 스펙트럼 모니터링, 군사 통신 감청 등에서 수신 신호가 어떤 변조(QPSK, QAM 등)인지 식별하는 데 사용됩니다.

## 필요한 툴박스
- HDL Coder
- Wireless HDL Toolbox / DSP HDL Toolbox (신호 프레임 처리)
- Deep Learning Toolbox (CNN 모델)
- 타사: Xilinx Vivado, Xilinx Zynq UltraScale+ MPSoC ZCU102 보드

## 딥러닝 변조 분류 개요
```
수신 신호 I/Q ──► [CNN] ──► 변조 종류 분류 확률
                (8/11개 클래스)
```
- CNN이 I/Q 성분을 입력으로 받아 변조 방식 분류
- 사전 학습된 모델 사용 (예: LTE 등 무선 신호 데이터셋)
- FPGA 배포: 소프트웨어보다 저지연·저전력으로 실시간 추론

## 하드웨어 배포 워크플로

### 1단계: CNN 준비
```matlab
% Deep Learning Toolbox로 사전 학습 CNN 로드/정의
net = ...;   % 변조 분류 CNN
analyzeNetwork(net);   % 네트워크 구조 확인
```

### 2단계: HDL 입력 신호 준비
- 수신 I/Q 신호를 CNN 입력 크기에 맞게 프레임화
- FPGA로 스트리밍되는 입력 데이터 모델링

### 3단계: FPGA 배포 (HDL Workflow Advisor)
1. 신호 처리 → CNN 추론 파이프라인 구성
2. **HDL Workflow Advisor**로 ZCU102 대상 설정
3. CNN 계층을 하드웨어(FPGA) 구현으로 매핑
4. HDL IP 코어 생성 → Vivado 합성·비트스트림
5. ZCU102 보드 배포

### HDL Workflow Advisor 단계 (ZCU102)
```matlab
% 보드 설정
hwboard = 'Xilinx Zynq UltraScale+ MPSoC ZCU102 Evaluation Kit';
% HDL Workflow Advisor에서:
%  1. Set Target Device (ZCU102)
%  2. Fixed-Point Conversion (CNN, 데이터 타입)
%  3. HDL Code Generation
%  4. IP Core Generation
%  5. Software Interface (AXI 레지스터)
%  6. Vivado Synthesis + Implementation
%  7. Deploy to target
```

## 검증
- FPGA에서 분류 결과와 MATLAB/Simulink 결과 비교
- 다양한 SNR에서 분류 정확도 확인
- 추론 지연(latency)·처리량 측정

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 실시간·저지연 추론 | 하드웨어 구현 복잡 |
| 저전력 (vs GPU/CPU) | CNN 자원(곱셈 누적) 큼 |
| 무선 신호 스펙트럼 모니터링 | 보드·툴 추가 필요 |

## 실행 요약
1. 사전 학습 CNN 로드·확인
2. I/Q 신호 프레임화 + CNN 추론 파이프라인 구성
3. HDL Workflow Advisor로 ZCU102 타겟 배포
4. FPGA에서 분류 정확도 검증

## 참고
- 공식 문서: Wireless HDL Toolbox → Modulation Classification by Using FPGA (since R2022b)
- 연관: 딥러닝 HDL 배포, FPGA prototyping, ZCU102 하드웨어 지원
