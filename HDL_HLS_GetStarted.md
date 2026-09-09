# HDL Coder 예제: MATLAB to High-Level Synthesis (HLS) Get Started

> 공식 예제: **"Get Started with MATLAB to High-Level Synthesis Workflow Using HDL Coder App"**
> 목적: HDL Coder 앱으로 MATLAB 설계에서 **고수준 합성(HLS) 코드(SystemC)** 를 생성하는 기초 워크플로.

## 개요
MATLAB로 작성한 설계에서 HDL Workflow Advisor를 통해 **고수준 합성(HLS) 코드(SystemC)** 를 생성하는 방법을 다룹니다. HDL Coder는 VHDL/Verilog 외에도 **합성 가능한 SystemC(HLS)** 코드와 HDL 테스트벤치 및 합성 스크립트를 생성할 수 있습니다. (예: Cadence Stratus HLS 연동)

## 필요한 툴박스
- HDL Coder
- Fixed-Point Designer
- (선택) 타사 HLS 툴: Cadence Stratus HLS

## HLS(High-Level Synthesis)란?
- C/C++/SystemC 등 **고수준 언어**를 합성 가능한 RTL로 자동 변환하는 설계 방법
- HDL Coder는 MATLAB → **합성 가능한 SystemC(HLS)** 코드 생성 지원
- 생성된 SystemC는 **Cadence Stratus HLS** 등 HLS 툴에서 합성

```
MATLAB 알고리즘
      │
      ▼  (HDL Coder, MATLAB Workflow Advisor)
고정소수점 변환
      │
      ▼
SystemC (IEEE 1666-2011, 합성 가능) ──► Cadence Stratus HLS ──► RTL
      │
      ▼
HDL 테스트벤치 / 검증
```

## 워크플로 (HDL Coder App)

### 1단계: MATLAB 설계 준비
HDL/HLS 지원 MATLAB 함수 작성:
```matlab
%#codegen
function y = myfilter(x)
    persistent state;
    if isempty(state), state = zeros(...); end
    % ... HDL/HLS 호환 연산
    y = ...;
end
```

### 2단계: HDL Coder App에서 프로젝트 생성
1. MATLAB 에디터 → **Apps 탭 → HDL Coder**
2. 프로젝트 생성, MATLAB 함수 추가
3. **Testbench** 추가

### 3단계: MATLAB Workflow Advisor 진행
| 단계 | 내용 |
|------|------|
| `1. Set Target` | **Target Language = SystemC/HLS**, 합성 툴 설정 |
| `2. Set Optimization` | HLS 최적화(파이프라이닝, 유니언 등) |
| `3. Define Input Types` | 입력 데이터 타입 지정 |
| `4. Fixed-Point Conversion` | 부동소수점 → 고정소수점 |
| `5. Generate HLS Code` | **SystemC(HLS) 코드 생성** |
| `6. Test Bench` | HLS 테스트벤치·시뮬레이션 스크립트 |

### HLS 코드 생성 (CLI)
```matlab
%% HLS 코드 생성 설정
cfg = coder.config('hdl');
cfg.TargetLanguage = 'SystemC';   % SystemC(HLS) 출력

% 프로그램 합성 가능한 SystemC 생성
hdlexport('myfilter', 'Config', cfg);
```

## 생성 파일
| 파일 | 내용 |
|------|------|
| `myfilter.cpp/.h` | 합성 가능한 SystemC 모델 |
| `myfilter_tb.cpp` | 테스트벤치 |
| `compile.do` | HLS 시뮬레이션 스크립트 |
| 리포트 | 고정소수점/HLS 생성 리포트 |

## MATLAB-to-SystemC for Cadence Stratus HLS
HDL Coder의 **MATLAB-to-SystemC 워크플로**:
- 고품질 RTL을 위한 합성 가능 SystemC/`sc_module` 생성
- Cadence Stratus HLS 합성 스크립트·제약 파일 생성
- 로직 합성 전 HLS 단계에서 아키텍처 탐색

## 실행 요약
1. HDL/HLS 호환 MATLAB 함수 작성
2. HDL Workflow Advisor에서 Target Language = SystemC(HLS)
3. 고정소수점 변환
4. HLS(SystemC) 코드 + 테스트벤치 생성
5. (선택) Cadence Stratus HLS로 합성

## 참고
- 공식 문서: https://www.mathworks.com/help/hdlcoder/gs/matlab-to-high-level-synthesis.html
- 연관: Get Started with MATLAB to HLS Using HDL Coder App, MATLAB to SystemC for Cadence Stratus HLS
