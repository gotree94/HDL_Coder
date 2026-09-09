# HDL Coder 예제: CORDIC atan2 Verilog → Simulink 역생성 (importhdl)

> 공식 예제: **"Generate Simulink Model from CORDIC Atan2 Verilog Code"**
> 목적: 기존 **Verilog 코드를 읽어 해당하는 Simulink 모델을 자동 생성**(역설계) — `importhdl` 함수 사용.

## 개요
이미 작성된 **Verilog 코드(CORDIC atan2 알고리즘)** 를 읽어와 그에 대응하는 **Simulink 모델로 자동 변환**합니다. HDL Coder의 `importhdl` 함수가 Verilog 파일을 파싱해 Simulink 블록 모델을 생성하는 **HDL 역설계** 기능을 보여줍니다.

## 필요한 툴박스
- HDL Coder
- Simulink

## importhdl이란?
`importhdl`은 **기존 HDL(Verilog/VHDL)을 Simulink 모델로 역생성**하는 함수:
- 이미 존재하는 HDL 설계를 Simulink 환경에서 유지보수·재사용하고 싶을 때 유용
- HDL로 검증된 IP를 시스템 수준 모델에 통합
- 하드웨어 설계를 고수준 모델로 문서화·리팩터링

## CORDIC atan2 알고리즘
CORDIC(COordinate Rotation Digital Computer)은 승산기 없이 **덧셈·뺄셈·시프트**만으로 atan2(각도)를 계산하는 하드웨어 친화적 알고리즘:
- 반복 회전으로 입력 벡터의 각도 계산
- 반복 횟수가 정밀도 결정

이 예제의 입력 Verilog는 CORDIC atan2 알고리즘을 구현한 코드입니다.

## 사용법

### 기본 구문
```matlab
importhdl('cordic_atan2.v');    % Verilog 파일 → Simulink 모델 생성
```

### 단계별 흐름
1. **Verilog 파일 준비**: CORDIC atan2 알고리즘을 포함한 `.v` 파일
2. **importhdl 호출**: Verilog 파싱 → 대응 Simulink 모델 자동 생성
3. **생성 모델 확인**: 블록 다이어그램으로 로직 구조 시각화
4. (선택) 생성 모델을 기반으로 재설계·수정

```matlab
% 예: 생성된 모델 로드
open_system('importhdl_cordic_atan2');
```

## 생성되는 Simulink 구조
- Verilog의 각 모듈/연산이 Simulink 블록으로 매핑
- 레지스터 → 지연 블록
- 산술 연산(덧셈·시프트) → 대응 블록
- 입력/출력 포트 → Inport/Outport

## 검증
- `importhdl`로 생성된 모델의 동작을 **원본 Verilog 시뮬레이션 결과와 비교**
- atan2 출력(각도)이 기존 참조와 일치하는지 확인

## 왜 유용한가?
| 활용 | 설명 |
|------|------|
| **기존 HDL 재사용** | HDL IP를 Simulink 시스템 모델에 통합 |
| **유지보수/문서화** | 하드웨어 로직을 고수준 블록 다이어그램으로 |
| **리팩터링** | HDL 기반 설계를 상위 레벨에서 개선·검증 |
| **시스템 통합** | HDL 블록 + 다른 Simulink 블록 함께 모델링 |

## 실행 요약
1. CORDIC atan2 Verilog 파일 준비
2. `importhdl`로 Simulink 모델 역생성
3. 생성 모델과 원본 HDL 동작 비교 검증

## 참고
- 공식 문서: HDL Coder → Generate Simulink Model from CORDIC Atan2 Verilog Code (Open Script)
- 연관: HDL 코드 생성, Simulink 모델 자동화
