# LBSTER 모델 개요

LBSTER는 생물학적 서열을 위한 여러 유형의 사전 훈련된 언어 모델을 제공하며, 각 모델은 서로 다른 목적으로 설계되었습니다. 이 페이지에서는 사용 가능한 모델 아키텍처 및 사전 훈련된 가중치에 대한 개요를 제공합니다.

## 모델 아키텍처

LBSTER는 여러 핵심 모델 아키텍처를 구현합니다:

### 1. 마스크 언어 모델 (LobsterPMLM)

이 모델은 마스크 언어 모델링 목표를 가진 BERT 아키텍처를 기반으로 합니다. 단백질 서열의 양방향 표현 학습을 위해 설계되었습니다.

**사용 사례**:
- 단백질 서열 임베딩
- 단백질 특성 예측
- 서열 분류
- 서열 유사성 검색

**구현**: `LobsterPMLM` 클래스

```python
from lobster.model import LobsterPMLM
model = LobsterPMLM("asalam91/lobster_24M")
```

### 2. 개념 병목 모델 (LobsterCBMPMLM)

이 모델은 해석 가능한 개념 병목 레이어로 마스크 언어 모델을 확장합니다. 다운스트림 작업의 성능을 유지하면서 단백질을 해석 가능한 생물학적 개념으로 인코딩할 수 있습니다.

**사용 사례**:
- 해석 가능한 단백질 표현 학습
- 제어 가능한 단백질 생성
- 개념 기반 서열 편집

**구현**: `LobsterCBMPMLM` 클래스

```python
from lobster.model import LobsterCBMPMLM
model = LobsterCBMPMLM("asalam91/cb_lobster_24M")
```

### 3. 인과 언어 모델 (LobsterPCLM)

이 모델은 GPT 모델과 유사한 자동 회귀 디코더 전용 아키텍처를 기반으로 합니다. 토큰별로 생물학적 서열을 생성하도록 설계되었습니다.

**사용 사례**:
- 단백질 서열 생성
- 단백질 설계
- 서열 완성

**구현**: `LobsterPCLM` 클래스

```python
from lobster.model import LobsterPCLM
model = LobsterPCLM.load_from_checkpoint("path/to/checkpoint.ckpt")
```

### 4. 구조 예측 모델 (LobsterPLMFold)

이 모델은 언어 모델 사전 훈련과 구조 예측 헤드를 결합하여 서열에서 단백질 구조를 예측할 수 있습니다.

**사용 사례**:
- 구조 예측
- 구조 기반 서열 설계

**구현**: `LobsterPLMFold` 클래스

## 사전 훈련된 모델

LBSTER는 다양한 크기와 훈련 목표를 가진 여러 사전 훈련된 모델을 제공합니다:

### 마스크 언어 모델

| 모델 | 파라미터 | 데이터셋 | 설명 | 링크 |
|-------|------------|---------|-------------|------|
| lobster_24M | 24M | UniRef50 | 24M 파라미터 단백질 마스크 LLM | [lobster_24M](https://huggingface.co/asalam91/lobster_24M) |
| lobster_150M | 150M | UniRef50 | 150M 파라미터 단백질 마스크 LLM | [lobster_150M](https://huggingface.co/asalam91/lobster_150M) |

### 개념 병목 모델

| 모델 | 파라미터 | 데이터셋 | # 개념 | 링크 |
|-------|------------|---------|------------|------|
| cb_lobster_24M | 24M | UniRef50+SwissProt | 718 | [cb_lobster_24M](https://huggingface.co/asalam91/cb_lobster_24M) |
| cb_lobster_150M | 150M | UniRef50+SwissProt | 718 | [cb_lobster_150M](https://huggingface.co/asalam91/cb_lobster_150M) |
| cb_lobster_650M | 650M | UniRef50+SwissProt | 718 | [cb_lobster_650M](https://huggingface.co/asalam91/cb_lobster_650M) |
| cb_lobster_3B | 3B | UniRef50+SwissProt | 718 | [cb_lobster_3B](https://huggingface.co/asalam91/cb_lobster_3B) |

## 모델 선택 가이드

사용할 LBSTER 모델을 선택할 때 다음을 고려하십시오:

1. **작업 요구 사항**:
   - 임베딩 또는 단백질 특성 예측 → 마스크 모델
   - 제어 가능한 생성 → 개념 병목 모델
   - 순수 생성 → 인과 모델
   - 구조 예측 → PLMFold 모델

2. **컴퓨팅 제약**:
   - 제한된 GPU 메모리 (≤8GB) → 24M 파라미터 모델
   - 중간 GPU 메모리 (≤16GB) → 150M 파라미터 모델
   - 높은 GPU 메모리 (≥24GB) → 650M 또는 3B 파라미터 모델

3. **속도 대 정확도 절충**:
   - 가장 빠른 추론 → 24M 파라미터 모델
   - 최고의 정확도 → 3B 파라미터 모델

## 사용 예제

### 사전 훈련된 모델 로드:

```python
# 마스크 언어 모델
from lobster.model import LobsterPMLM
mlm = LobsterPMLM("asalam91/lobster_24M")

# 개념 병목 모델
from lobster.model import LobsterCBMPMLM
cbm = LobsterCBMPMLM("asalam91/cb_lobster_24M")

# 인과 언어 모델
from lobster.model import LobsterPCLM
clm = LobsterPCLM.load_from_checkpoint("path/to/checkpoint.ckpt")
```

### 기본 추론:

```python
# 마스크 언어 모델로 서열 인코딩
sequence = "MVLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHF"
tokens = mlm.tokenizer(sequence, return_tensors="pt")
embeddings = mlm.model(input_ids=tokens["input_ids"],
                        attention_mask=tokens["attention_mask"])
cls_embedding = embeddings[:, 0, :]  # [CLS] 토큰 임베딩 가져오기

# 개념 병목 모델에서 개념 가져오기
tokens = cbm.tokenizer(sequence, return_tensors="pt")
outputs = cbm.model(input_ids=tokens["input_ids"],
                    attention_mask=tokens["attention_mask"],
                    inference=True)
concepts = outputs["concepts"]  # 개념 값 가져오기
```

다음 섹션에서는 각 모델 유형을 더 자세히 살펴보겠습니다.
