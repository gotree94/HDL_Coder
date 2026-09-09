# HDL Coder 예제: Symmetric FIR Filter (HDL Workflow Advisor)

> 공식 예제: **"Generate and Synthesize HDL Code for Symmetric FIR Filter Using the HDL Workflow Advisor"**
> 목적: HDL Coder 프로젝트를 만들고 MATLAB HDL Workflow Advisor를 이용해 **대칭(Symmetric) FIR 필터** 설계에서 HDL 코드를 생성하고 합성하는 전 과정.

## 개요
MATLAB으로 작성한 대칭 FIR(Finite Impulse Response) 필터 알고리즘에서 HDL Coder 프로젝트를 만들고, HDL Workflow Advisor를 통해:
1. HDL 코드 생성
2. HDL 테스트벤치로 검증
3. 타사 합성 툴(예: ModelSim + Xilinx Vivado)로 합성

대칭 계수를 활용한 **CSD(Canonical Signed Digit) 기법**으로 승산기를 시프트-덧셈으로 대체해 하드웨어 면적을 크게 줄이는 것이 핵심입니다.

## 필요한 툴박스
- HDL Coder
- Fixed-Point Designer
- (선택) DSP System Toolbox / Signal Processing Toolbox — 필터 설계
- (합성) 타사 툴: ModelSim(시뮬레이션), Xilinx Vivado(합성)

## 알고리즘: 대칭 FIR 필터
선형 위상 FIR 필터는 계수가 대칭적입니다. 이 대칭성을 이용하면 **승산기 수를 절반으로 줄일** 수 있습니다.

```
y[n] = Σ b[k]·x[n-k]      (k = 0..N-1, N = 탭 수)

대칭 조건: b[k] = b[N-1-k]
→ 대칭 위치의 입력을 먼저 더한 뒤 공통 계수로 곱함
→ 승산기 수 N/2개로 감소
```

### MATLAB 설계 예 (`mlhdlc_sfir.m`)
```matlab
%#codegen
function [y_out, a1, a2, b1, b2] = mlhdlc_sfir(x_in)
    % 대칭 FIR 필터 설계
    persistent coff1 coff2;
    if isempty(coff1)
        % 계수 (대칭 배열)
        coff1 = ... ;  % 설계 계수
        coff2 = ... ;
    end
    % 대칭 덧셈 후 승산 -> 면적 절감
    ...
end
```

## HDL Workflow Advisor 사용 절차

### 1단계: 프로젝트 생성 (CLI)
```matlab
hdlcoderproject('sfir_fixed');   % 또는 GUI로 생성
```
- **Add MATLAB function** → `mlhdlc_sfir.m`
- **Add files** → 설계 + 테스트벤치

### 2단계: HDL Workflow Advisor 단계별 진행
| 단계 | 내용 |
|------|------|
| `1. Set Target` | 합성 툴 선택(Refresh list로 Vivado 등록), 디바이스 지정 |
| `2. Set Optimization` | HDL 최적화 옵션(속도/면적) |
| `3. Define Input Types` | `x_in` 입력 데이터 타입 정의 |
| `4. Fixed-Point Conversion` | 부동소수점 → 고정소수점 변환 |
| `5. HDL Code Generation` | HDL 코드 생성 |
| `6. HDL Test Bench` | 테스트벤치 생성·시뮬레이션 |
| `7. FPGA Synthesis` | Vivado로 합성·구현 |

## CSD 곱셈 최적화 (면적 최적화 핵심)
- **CSD(Canonical Signed Digit)**: 계수 승산기를 **덧셈과 시프트**로 변환 → 승산기(DSP 슬라이스) 대신 로직(LUT) 사용
- FIR 계수(예: gain, 필터 계수)에 CSD 적용 시 하드웨어 면적 크게 감소
- HDL Coder의 `CoeffMultipliers` 파라미터:
  - `'csd'` — 계수 곱셈을 시프트-덧셈으로 변환 (승산기 수 감소)
  - `'factored-csd'` — 계수의 소인수를 이용해 더 큰 면적 감소 (클록 속도는 다소 저하)
  - `'multipliers'` (기본) — 승산기 유지
  - 참고: CSD는 **완전병렬(Fully Parallel)** 필터 구현에서만 지원 (완전직렬/부분직렬은 미지원)

### CSD 사용 예
```matlab
% 모델에서 Discrete FIR Filter 또는 Gain 블록의 HDL Block Properties 설정
% CoeffMultipliers: 'csd' 로 지정한 후 makehdl 실행
```

## 합성 결과 확인
합성 후 **Resource Utilization Report**에서:
- LUT, FF, DSP 슬라이스 사용량
- CSD 적용 전후 면적 비교

## 최종 산출물
- 설계 HDL + 테스트벤치 HDL
- 합성 스크립트
- resource/타이밍 리포트

## 실행 요약
1. MATLAB 대칭 FIR 설계 함수 작성
2. HDL Workflow Advisor로 고정소수점 변환 → HDL 생성 → 테스트벤치 검증
3. Vivado로 합성
4. **CSD 기법**으로 승산기→시프트/덧셈 변환해 면적 최적화

## 참고
- 공식 문서: https://www.mathworks.com/help/hdlcoder/gs/basic-hdl-code-generation-with-the-workflow-advisor.html
- 연관: Resource Sharing for Area Optimization, HDL Filter Block Properties
