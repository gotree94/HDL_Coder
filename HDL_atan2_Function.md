# HDL Coder 예제: Implement atan2 Function for HDL

> 공식 예제: **"Implement atan2 Function for HDL"**
> 목적: `Complex to Magnitude Angle` 블록을 이용해 **atan2 함수**를 하드웨어로 구현 (CORDIC 기반).

## 개요
복소 신호의 **각도(phase/angle)** 를 계산하는 `atan2` 함수를 하드웨어로 구현합니다. DSP HDL Toolbox의 **`Complex to Magnitude Angle` 블록**을 사용하며, 내부적으로 CORDIC(Coordinate Rotation Digital Computer) 알고리즘을 사용해 삼각 함수를 하드웨어 친화적인 반복 연산으로 계산합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- HDL Coder
- Simulink

## atan2란?
```
atan2(y, x) = 신호 (x, y)가 x축과 이루는 각도 [−π, π]
```
- 복소 신호의 위상(phase) 추출에 필수
- 레이더/통신의 위상 검출, 방향 탐지, 동기화 등에 활용
- 사분면별 각도를 올바르게 반환(y/x의 부호에 따라)

## CORDIC 알고리즘
`atan2`를 하드웨어에서 구현하는 표준 방법이 **CORDIC**입니다:
- 승산기 없이 **덧셈·뺄셈·시프트**만으로 각도 회전 반복
- `Number of iterations`(반복 횟수)로 정밀도와 자원 트레이드오프
- 반복이 많을수록 정밀도 ↑, 자원 ↑

### CORDIC 회전 반복
```
각 샘플마다:
  if y >= 0:  회전 각도 감소 -> x,y 회전, 각도 누적 +
  else:       회전 각도 증가 -> x,y 회전, 각도 누적 -
  반복 횟수만큼 수행 → 누적 각도 = atan2(y,x)
```

## 모델 구성
- **`Complex to Magnitude Angle` 블록** (DSP HDL Toolbox):
  - 입력: 복소 신호(실수 + 허수)
  - 출력1: **Magnitude(크기)** — √(x²+y²)
  - 출력2: **Angle(각도)** — atan2 결과 [−π, π]
- CORDIC 반복 횟수 설정으로 정밀도 조절
- 고정소수점 데이터 타입

## HDL 코드 생성
```matlab
makehdl('모델명/Atan2_DUT');   % CORDIC atan2 하드웨어 생성
```

## 하드웨어 특징
- **승산기 불필요**: 시프트 + 덧셈만 사용 → 작은 면적
- **파이프라인/반복 구조**: 반복 횟수만큼 스테이지
- 입력 유효 신호(`valid`)와 함께 스트리밍 처리
- 위상 각도를 도/라디안으로 출력 옵션

## 검증
- MATLAB의 `atan2` 레퍼런스와 하드웨어 출력 비교
- 반복 횟수에 따른 각도 정밀도 오차 확인
- 각 사분면에서 올바른 각도(부호) 확인

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 승산기 없이 소형 구현 | 반복 횟수에 따라 지연 증가 |
| 높은 정밀도 (반복 증가 시) | 반복 증가 시 자원 증가 |
| 신호 위상/크기 동시 계산 | 고정소수점 근사 오차 |

## 실행 요약
1. `Complex to Magnitude Angle` 블록으로 atan2 구성
2. CORDIC 반복 횟수 설정
3. 레퍼런스 atan2와 각도 정밀도 검증
4. `makehdl`로 HDL 생성

## 참고
- 공식 문서: DSP HDL Toolbox → Implement atan2 Function for HDL (Open Model)
- 연관: Complex to Magnitude Angle 블록, CORDIC 관련 하드웨어
