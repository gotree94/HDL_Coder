# HDL Coder 예제: Simulink Counter → HDL 코드 생성 (Get Started)

> 공식 예제: **"Generate HDL Code from Simulink Model"**
> 목적: Simulink에서 만든 간단한 카운터 모델의 HDL 코드(VHDL/Verilog/SystemVerilog)를 생성하는 기초 과정.

## 개요
Simulink로 모델링한 **동기식 카운터**에서 HDL Coder를 이용해 합성 가능한 HDL 코드를 생성합니다. 이전 MATLAB Counter 예제와 함께 HDL Coder의 가장 기본적인 시작 예제 중 하나입니다.

## 필요한 툴박스
- HDL Coder
- Simulink

## 모델 구조 (Simple Counter Model)
카운터 모델은 0부터 설정한 임계값(`count_threshold = 15`)까지 카운트한 뒤 0으로 랩어라운드합니다.

### 주요 신호/포트
| 신호 | 역할 |
|------|------|
| `count_threshold` | 카운팅 상한값(기본 15) — Constant 블록으로 설정 |
| `Enable` | 카운팅 제어 — 1이면 증가, 0이면 이전 값 유지 |
| `HDL_DUT` | HDL 코드를 생성할 **DUT(Design Under Test)** 서브시스템 |
| 출력 | 현재 카운트 값 |

- 카운팅 상한값은 `count_threshold` 포트에 연결된 Constant 블록값을 바꿔 변경 가능
- `Enable` 포트에 연결된 Constant: 1 → 카운트 증가, 0 → 카운트 유지

## HDL 코드 생성 워크플로

### 1단계: HDL 호환 모델 만들기
먼저 **"Create HDL-Compatible Simulink Model"** 예제를 따라 HDL 호환 모델을 생성합니다.
- Simulink **Model Templates** 활용 가능:
  - registers, ROM, 기본 산술 연산, 복소 승산기, 시프트 레지스터 등
  - MATLAB 툴스트립 → Simulink → HDL Coder 섹션의 템플릿 선택

### 2단계: HDL 코드 생성
`HDL_DUT` 서브시스템을 DUT로 지정해 코드를 생성합니다.

```matlab
% 작업 폴더에 모델 로드
open_system('gm_hdlcoder_simple_up_counter');

% DUT에 대한 HDL 코드 생성
makehdl('gm_hdlcoder_simple_up_counter/HDL_DUT');

% SystemVerilog로 생성하려면
% HDL_DUT의 HDL Block Properties에서 TargetLanguage = 'SystemVerilog' 설정 후
% makehdl 실행
```

### 생성되는 파일
| 파일 | 내용 |
|------|------|
| `HDL_DUT.vhd` | VHDL 엔티티 + RTL 아키텍처 (Verilog면 `.v`, SystemVerilog면 `.sv`) |
| `HDL_DUT_compile.do` | ModelSim 컴파일 스크립트 |
| `HDL_DUT_map.txt` | 생성된 HDL 모듈 ↔ 모델 서브시스템 매핑 (코드 추적) |
| `HDL_DUT_report.html` | HDL 체크 리포트 (경고/메시지) |
| `gm_hdlcoder_simple_up_counter.slx` | 생성된 HDL을 동작 모사하는 모델 |
| `.hcv` | HDLCode View 파일 (Simulink에서 생성 코드 보기용) |

### 코드 생성 후 확인
- **Code View**: Simulink에서 'View Code'를 눌러 생성 HDL 확인
- **생성 모델**: `gm_hdlcoder_simple_up_counter`로 열어 블록 구현과 최적화 효과 확인
- **Trace**: mapping 파일로 Simulink 블록 ↔ HDL 코드 라인 대응 추적

## 핵심 포인트
1. **DUT 서브시스템**을 명확히 지정 — HDL 생성 단위
2. **HDL 호환 모델** 여부를 미리 확인 (HDL Advisor/Model Checker)
3. **Simulink 템플릿**으로 레지스터·ROM·연산 등을 빠르게 구성
4. 생성 모델(`.slx`)로 HDL 동작을 Simulink 환경에서 재현·검증 가능

## 참고
- 공식 문서: https://www.mathworks.com/help/hdlcoder/gs/example-generating-hdl-code-from-a-simulink-model.html
- 연관 예제: Create HDL-Compatible Simulink Model
