# HDL Coder 예제: FFT 기반 IFFT 구현 (FFT 블록 재사용)

> 공식 예제: **"Implement an Inverse FFT by Using a Forward FFT Block"**
> 목적: 별도 IFFT 블록 대신 **전방 FFT 블록을 재사용**하여 IFFT를 구현 — 자원 절약.

## 개요
전용 IFFT 블록을 배치하는 대신 **하나의 FFT 블록을 재사용**하여 IFFT를 계산하는 하드웨어 구현을 보여줍니다. FFT와 IFFT를 동시에 사용하지 않는 응용(시간 분할 사용 가능한 설계)에서 하드웨어 자원이 제한된 FPGA/ASIC에 적합합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- HDL Coder
- Simulink
- Fixed-Point Designer

## 수학적 원리: FFT로 IFFT 계산
DFT/IDFT의 대칭성을 이용:
```
IFFT(X) = (1/N) · conj( FFT( conj(X) ) )
```
즉, 입력의 공액(conjugate) → FFT → 다시 공액 → 1/N 스케일링으로 IFFT를 얻을 수 있습니다.

### 구현 단계 (FFT 블록 단일 사용)
1. **입력 공액화**: 입력의 허수부 부호 반전
2. **FFT 수행**: 기존 FFT 블록으로 변환
3. **출력 공액화**: 결과 허수부 부호 반전
4. **1/N 스케일링**: FFT 크기로 나눔

```
X ──► [conj] ──► [FFT] ──► [conj] ──► [×1/N] ──► IFFT(X)
```

## 모델 구성 (FFTIFFT_Streaming)
- **FFTIFFT_Streaming 서브시스템**: DSP HDL Toolbox의 FFT 블록 + 공액(swapping) 로직
- **FFT/IFFT 모드 토글 스위치**:
  - `FFT_IFFT_Control = 0` → **IFFT 모드**
  - `FFT_IFFT_Control = 1` → **FFT 모드**
- 실수/허수부 교환(swap) 로직으로 공액 연산 수행

### 모드 제어
```matlab
FFT_IFFT_Control = 0;  % IFFT 모드
FFT_IFFT_Control = 1;  % FFT 모드
```

## 검증
- 구현한 FFT-기반 IFFT의 출력을 **표준 IFFT 블록(DSP HDL Toolbox)** 결과와 비교
```matlab
IFFTOutput = squeeze(dataOut(:,:,validOut));
figure;
plot(real(IFFTOutput(:)));
title('IFFT Output Signal');
grid on;
```
- 참조 표준 IFFT 블록과 출력 일치 확인

## HDL 코드 생성
```matlab
makehdl('모델명/FFTIFFT_Streaming');
```

## 하드웨어 절약 효과
- IFFT 전용 블록(하드웨어) 불필요 → **승산기·BRAM 절감**
- FFT 블록 하나로 FFT/IFFT 양방향 지원
- 자원이 제한된 설계(예: 센서·소형 통신)에 적합
- 대신 FFT와 IFFT를 **동시에** 필요로 하는 응용에는 부적합 (시분할 필요)

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 별도 IFFT 하드웨어 불필요 → 자원 절감 | FFT·IFFT 동시 동작 불가(시분할 필요) |
| 자원 제한 설계에 적합 | 추가 공액/스케일링 로직 |
| FFT/IFFT 모드 전환 융통성 | 제어 로직 필요 |

## 실행 요약
1. FFT 블록 + 공액(swap) 로직으로 FFTIFFT 구현
2. 모드 토글 스위치로 FFT/IFFT 선택
3. 표준 IFFT 블록과 출력 비교 검증
4. `makehdl`로 HDL 생성

## 참고
- 공식 문서: DSP HDL Toolbox → FFT 블록 문서의 "Implement an inverse FFT using a forward FFT block" 예제
- 연관: Implement FFT Algorithm for FPGA, IFFT 블록 문서
