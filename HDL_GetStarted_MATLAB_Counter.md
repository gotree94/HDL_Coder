# HDL Coder 예제: MATLAB Counter → HDL 코드 생성 (Get Started)

> 공식 튜토리얼: **"Generate HDL Code from MATLAB Algorithms"**
> 목적: MATLAB 함수로 작성한 4-bit 동기식 업 카운터에서 HDL 코드를 생성하는 기초 과정.

## 개요
MATLAB에서 작성한 알고리즘 함수(`mlhdlc_counter.m`)를 HDL Coder의 **HDL Workflow Advisor**를 이용해 고정소수점으로 변환한 뒤 **VHDL/Verilog HDL 코드**를 생성합니다. HDL Coder 입문의 첫 걸음 격인 예제입니다.

## 필요한 툴박스
- HDL Coder
- Fixed-Point Designer (고정소수점 변환)
- MATLAB

## 알고리즘 코드 (`mlhdlc_counter.m`)
```matlab
%#codegen
function count = mlhdlc_counter(enable_ctr)
persistent count_val;
if isempty(count_val)
    count_val = 0;
end
if enable_ctr
    count_val = count_val + 1;
    if count_val > 15
        count_val = 0;
    end
end
count = count_val;
```

**동작**: `enable_ctr`가 0이 아닐 때 `count_val`이 1씩 증가. 15에 도달하면 0으로 리셋(4-bit 카운터, 0~15 순환).

### 코드 작성 모범 사례 (이 예제가 보여주는 것)
1. **HDL/HLS 지원 함수만 사용** — 코드 생성이 지원되는 MATLAB 함수/구문만 사용
2. **persistent 변수 초기화** — `if isempty(count_val)`로 첫 호출 시점에 초기값 지정
   - persistent 변수는 HDL에서 **레지스터(register)** 로 매핑됨
3. **고정소수점 호환 로직** — 증감/비교 연산만 사용

## 테스트벤치 (`mlhdlc_counter_tb.m`)
생성 전 MATLAB 코드가 코드 생성에 적합한지 검증하는 테스트벤치입니다.
```matlab
expected_output = zeros(1, 40);
actual_output = zeros(1, 40);
enable_ctr = 1;
for ii = 1:40
    actual_output(ii) = mlhdlc_counter(enable_ctr);
    expected_output(ii) = mod(ii-1, 16);   % 0~15 반복 기대값
end
% 그래프로 실제 vs 기대 출력 비교
```
- 기대 출력: `mod(ii-1, 16)` → 0,1,2,...,15,0,1,... 패턴
- 실제 출력: `mlhdlc_counter` 함수 결과
- 두 값의 그래픽 비교로 알고리즘의 정확성을 확인

## HDL 코드 생성 워크플로 (HDL Workflow Advisor)

### 1단계: 프로젝트 생성
```matlab
% MATLAB Command Window
open mlhdlc_counter
open mlhdlc_counter_tb
```
1. MATLAB 에디터 → **Apps 탭 → HDL Coder** 클릭
2. 프로젝트 이름: `mlhdlc_counter`, 위치: 현재 작업 폴더
3. **Add MATLAB function** → `mlhdlc_counter.m` 선택
4. **Add files** → 설계·테스트벤치 파일 추가

### 2단계: HDL Workflow Advisor 실행
`Step 1.1`부터 순서대로 진행:
1. **Set Target Device and Synthesis Tool** — 합성 툴 선택 (예: Xilinx Vivado), FPGA 디바이스 설정
2. **Set Optimization Options** — 최적화 옵션 설정
3. **Define Input Types** — 입력(`enable_ctr`) 데이터 타입 정의
4. **Fixed-Point Conversion** — 부동소수점 MATLAB 코드를 고정소수점으로 변환
5. **HDL Code Generation** — `count = mlhdlc_counter` DUT에 대한 HDL 생성
6. **HDL Test Bench Generation** — 테스트벤치 생성

### 3단계: 코드 생성 명령 (CLI)
```matlab
%% 고정소수점 설계로 변환
design_cfg = coder.config('hdl');
design_cfg.TargetLanguage = 'Verilog';   % 또는 'VHDL'
% ...
hdlexport('mlhdlc_counter');   % HDL 생성
```

### 생성되는 파일
VHDL 생성 시:
- `mlhdlc_counter.vhd` — 엔티티 + RTL 아키텍처
- `mlhdlc_counter_tb.vhd` — 테스트벤치
- `mlhdlc_counter_compile.do` — ModelSim 컴파일 스크립트
- `mlhdlc_counter_map.txt` — 모델↔HDL 매핑 파일
- `mlhdlc_counter_report.html` — 생성 리포트

## HDL에서의 의미
- `persistent count_val` → **레지스터(count_reg)** 로 합성
- `count_val + 1` → **증가 로직**
- `if enable_ctr` → **Enable 제어**
- `count_val > 15 → 0` → **리셋/랩어라운드**

## 실행 요약
1. MATLAB 함수 작성 (`mlhdlc_counter.m`) — 4-bit 카운터
2. 테스트벤치로 알고리즘 검증 (`mlhdlc_counter_tb.m`)
3. HDL Workflow Advisor → 고정소수점 변환 → HDL 생성
4. 생성된 VHDL/Verilog 확인 및 합성

## 참고
- 공식 문서: https://www.mathworks.com/help/hdlcoder/gs/generate-hdl-code-from-matlab.html
- 이 예제는 HDL Coder를 처음 접할 때 시작하기 가장 좋은 기초 튜토리얼입니다.