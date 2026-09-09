# HDL Coder 예제: Frequency-Domain Filtering in HDL

> 공식 예제: **"Frequency-Domain Filtering in HDL"**
> 목적: DSP HDL Toolbox의 **FFT와 IFFT 블록**을 이용해 주파수 영역 필터링을 구현 (**overlap-add 기법**).

## 개요
주파수 영역에서 필터링을 수행하는 하드웨어 설계를 구현합니다. 필터는 DSP HDL Toolbox의 **FFT와 IFFT 블록**으로 구성되며, sinusoidal 입력을 **overlap-add 방법**으로 필터링합니다.

## 필요한 툴박스
- DSP HDL Toolbox
- Fixed-Point Designer
- Simulink
- (HDL 생성) HDL Coder

## 왜 주파수 영역 필터링인가?
**시간 영역 convolution**은 탭 수 N에 비례하는 연산량:
```
y[n] = Σ b[k]·x[n-k]   →  O(N) per sample
```
**주파수 영역 필터링**(FFT 기반)은 고차(탭 수 많은) 필터에서 훨씬 효율적:
```
x[n] → FFT → (주파수 응답 곱) → IFFT → y[n]
```
- **overlap-add / overlap-save** 기법이 널리 사용
- **고차 필터(탭 수 큰 FIR)** 에서 면적 효율이 크게 향상

### Overlap-Add 방법
1. 입력을 블록으로 나눔
2. 각 블록을 FFT
3. 주파수 응답(필터 계수의 FFT)과 곱함
4. IFFT로 시간 영역 복원
5. 블록 간 **겹치는 부분(overlap)** 을 더해 올바른 convolution 재구성

> **요건**: 필터 길이가 벡터 크기로 나누어져야 함. 2-샘플 벡터의 경우 **필터 길이 % 2 == 0** (짝수 탭)이어야 함.

## 모델 구성 (300-tap 필터 예시)
- **300탭의 완전 대칭 저역통과 필터** 설계
- 입력: **chirp(스윕 톤)** 신호 → 저역통과 특성 시각화가 용이
- **밀도 계수(density factor)**: 필터 수렴을 위해 기본값보다 큰 값으로 설계
- 필터 전달함수는 저역통과(low-pass)
- FFT + IFFT 블록으로 주파수 영역 필터링 수행

```
Input ──► [FFT] ──► [× 주파수응답] ──► [IFFT] ──► Output
              (overlap-add 제어 포함)
```

## 대안: Frequency-Domain FIR Filter 블록
DSP System Toolbox의 **`Frequency-Domain FIR Filter` 블록**을 사용할 수도 있습니다:
- overlap-save / overlap-add 방식 지원
- **분자(numerator) 분할(partition)** 옵션으로 **지연(latency) 감소**
- 주파수 응답을 직접 계수로 지정 가능 (필터 계수의 FFT를 주파수 응답으로)

### 주파수 응답 계수 지정 예
```matlab
order = 400;
Fs = 8000;
Fcutoff = 2000;
imp = designLowpassFIR(FilterOrder=order, CutoffFrequency=2*Fcutoff/Fs);
H = fft(imp, 2*numel(imp));   % 필터 임펄스 응답의 FFT → 주파수 응답
```
- 시간 영역 FIR 블록과 비교 시, 주파수 영역 블록의 **지연만큼 입력을 지연**시켜 출력이 정확히 일치함을 확인

## HDL 코드 생성
```matlab
% FFT/IFFT DUT 서브시스템 지정 후
makehdl('모델명/FreqDomainFilter_DUT');
```

## 장단점 정리
| 장점 | 단점 |
|------|------|
| 고차 필터에서 면적·계산 효율 우월 | FFT 하드웨어(가산기·트위들 ROM) 필요 |
| overlap-add로 긴 응답 처리 가능 | 파이프라인 지연 발생 |
| FFT 블록 재사용(GSPS 스트리밍) | 제어 로직 복잡 |

## 실행 요약
1. FFT + IFFT 블록으로 주파수 영역 필터 구성
2. overlap-add 방식으로 convolution 구현
3. chirp 입력으로 저역통과 응답 확인
4. `makehdl`로 HDL 생성

## 참고
- 공식 문서: https://www.mathworks.com/help/dsphdl/ug/frequency-domain-filtering.html
- 연관: Implement FFT Algorithm for FPGA, Frequency-Domain FIR Filter block
