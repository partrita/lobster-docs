# LBSTER의 토큰화

토큰화는 원시 생물학적 서열을 모델이 처리할 수 있는 숫자 토큰으로 변환하는 언어 모델의 중요한 전처리 단계입니다. LBSTER는 다양한 유형의 생물학적 서열에 대한 전문 토크나이저를 제공합니다.

## 토크나이저 유형

LBSTER는 다양한 생물학적 서열 유형에 대한 여러 토크나이저 구현을 포함합니다:

### 1. 아미노산 토크나이저

`AminoAcidTokenizerFast` 토크나이저는 단백질 서열을 위해 설계되었으며 각 아미노산을 별도의 토큰으로 토큰화합니다.

```python
from lobster.tokenization import AminoAcidTokenizerFast

tokenizer = AminoAcidTokenizerFast()
tokens = tokenizer("MVLSPADKTNVKAAWG", return_tensors="pt")
print(tokens["input_ids"])
```

### 2. 뉴클레오티드 토크나이저

`NucleotideTokenizerFast` 토크나이저는 DNA 및 RNA 서열에 특화되어 있으며 각 뉴클레오티드(A, C, G, T/U)를 별도의 토큰으로 토큰화합니다.

```python
from lobster.tokenization import NucleotideTokenizerFast

tokenizer = NucleotideTokenizerFast()
tokens = tokenizer("ATGCGATCGATCGATCG", return_tensors="pt")
print(tokens["input_ids"])
```

### 3. SMILES 토크나이저

`SmilesTokenizerFast` 토크나이저는 화학 분자를 나타내는 SMILES 문자열을 처리하여 화학적으로 의미 있는 토큰으로 분해합니다.

```python
from lobster.tokenization import SmilesTokenizerFast

tokenizer = SmilesTokenizerFast()
tokens = tokenizer("CC(=O)OC1=CC=CC=C1C(=O)O", return_tensors="pt")  # 아스피린
print(tokens["input_ids"])
```

### 4. PMLM 토크나이저

`PmlmTokenizer`는 LBSTER의 단백질 언어 모델에 대한 기본 토크나이저이며 ESM 스타일 토큰화와 호환됩니다.

```python
from lobster.tokenization import PmlmTokenizer
import importlib.resources

path = importlib.resources.files("lobster") / "assets" / "pmlm_tokenizer"
tokenizer = PmlmTokenizer.from_pretrained(path, do_lower_case=False)
tokens = tokenizer("MVLSPADKTNVKAAWG", return_tensors="pt")
print(tokens["input_ids"])
```

### 5. MGM 토크나이저

`MgmTokenizer`는 아미노산, 뉴클레오티드 및 SELFIES를 포함한 여러 양식을 지원하는 다중 모드 유전체학 모델 토크나이저입니다.

```python
from lobster.tokenization import MgmTokenizer

tokenizer = MgmTokenizer(model_max_length=512)
tokens = tokenizer("ATGCGATCGATCGATCG", return_tensors="pt")
print(tokens["input_ids"])
```

## 토크나이저 변환

LBSTER는 데이터 파이프라인과의 쉬운 통합을 위해 토크나이저 주위에 변환 래퍼를 제공합니다:

### 기본 토크나이저 변환

```python
from lobster.transforms import TokenizerTransform
from lobster.tokenization import AminoAcidTokenizerFast

transform = TokenizerTransform(
    tokenizer=AminoAcidTokenizerFast(),
    padding="max_length",
    max_length=512,
    truncation=True
)

# 데이터셋과 함께 사용 가능
tokens = transform("MVLSPADKTNVKAAWG")
```

### 개념 인식 토크나이저 변환

```python
from lobster.tokenization import PmlmConceptTokenizerTransform
import importlib.resources

path = importlib.resources.files("lobster") / "assets" / "pmlm_tokenizer"
transform = PmlmConceptTokenizerTransform(
    path,
    padding="max_length",
    truncation=True,
    max_length=512,
    normalize=True
)

# 토큰과 개념 값을 모두 반환
result = transform("MVLSPADKTNVKAAWG")
tokens = result["input_ids"]
concepts = result["all_concepts"]
```

## 특수 토큰

LBSTER 토크나이저는 여러 특수 토큰을 사용합니다:

- `<cls>`: 서열 시작 토큰
- `<pad>`: 패딩 토큰
- `<eos>`: 서열 종료 토큰
- `<unk>`: 알 수 없는 토큰
- `<mask>`: 마스크 토큰 (마스크 언어 모델링용)
- `<sep>`: 구분자 토큰 (다중 서열 작업용)

특정 토크나이저에는 추가 특수 토큰이 있을 수 있습니다.

## 어휘 크기

서로 다른 토크나이저는 서로 다른 어휘 크기를 가집니다:

- 아미노산 토크나이저: 33개 토큰 (20개 표준 아미노산 + 특수 토큰)
- 뉴클레오티드 토크나이저: 12개 토큰 (4개 뉴클레오티드 + 특수 토큰)
- PMLM 토크나이저: 사전 훈련된 모델에 따라 다르며 일반적으로 30-33개 토큰
- MGM 토크나이저: 구성에 따라 가변적

## 모델과 함께 토크나이저 사용

LBSTER 모델은 해당 토크나이저로 인스턴스화됩니다:

```python
from lobster.model import LobsterPMLM

# 모델은 적절한 토크나이저와 함께 제공됩니다
model = LobsterPMLM("asalam91/lobster_24M")
tokenizer = model.tokenizer

# 서열 토큰화
tokens = tokenizer("MVLSPADKTNVKAAWG", return_tensors="pt")

# 모델을 통한 순방향 패스
outputs = model.model(input_ids=tokens["input_ids"],
                      attention_mask=tokens["attention_mask"])
```

## 사용자 지정 토큰화 워크플로

고급 사용 사례의 경우 사용자 지정 토큰화 파이프라인을 구축할 수 있습니다:

```python
from lobster.tokenization import PmlmTokenizer
import importlib.resources
import torch

path = importlib.resources.files("lobster") / "assets" / "pmlm_tokenizer"
tokenizer = PmlmTokenizer.from_pretrained(path, do_lower_case=False)

def batch_tokenize(sequences, max_length=512):
    """패딩으로 시퀀스 배치 토큰화"""
    encodings = [tokenizer.encode(seq) for seq in sequences]

    # 이 배치의 최대 길이 결정
    batch_max_len = min(max(len(enc) for enc in encodings), max_length)

    # 시퀀스 패딩
    padded_encodings = []
    attention_masks = []

    for enc in encodings:
        # 필요한 경우 자르기
        if len(enc) > max_length:
            enc = enc[:max_length]

        # 어텐션 마스크 생성 (토큰의 경우 1, 패딩의 경우 0)
        attention_mask = [1] * len(enc) + [0] * (batch_max_len - len(enc))
        attention_mask = attention_mask[:max_length]

        # 시퀀스 패딩
        padded_enc = enc + [tokenizer.pad_token_id] * (batch_max_len - len(enc))
        padded_enc = padded_enc[:max_length]

        padded_encodings.append(padded_enc)
        attention_masks.append(attention_mask)

    return {
        "input_ids": torch.tensor(padded_encodings),
        "attention_mask": torch.tensor(attention_masks)
    }
```

다음 섹션에서는 각 토크나이저 유형을 더 자세히 살펴보겠습니다.
