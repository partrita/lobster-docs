# 설치

시스템에 LBSTER를 설치하는 방법에는 여러 가지가 있습니다. 사용자 환경에 가장 적합한 방법을 선택하십시오.

## `uv` 사용

[`uv`](https://github.com/astral-sh/uv)는 빠른 파이썬 패키지 설치 및 해결 프로그램입니다. 이 방법이 권장되는 설치 방법입니다:

```bash
# 새 가상 환경 만들기
uv venv --python 3.12

# 가상 환경 활성화
source .venv/bin/activate

# LBSTER 설치
uv pip install -e .
```

## `mamba` 또는 `conda` 사용

conda 환경을 선호하는 경우:

```bash
# 저장소 복제
git clone https://github.com/prescient-design/lobster.git
cd lobster

# 환경 생성 및 활성화
mamba env create -f env.yml
conda activate lobster

# 개발 모드로 설치
pip install -e .
```

## 의존성

LBSTER에는 다음과 같은 핵심 의존성이 있습니다:

- Python >= 3.10
- torch
- lightning
- transformers >= 4.24.0
- biopython
- pandas
- scipy
- hydra-core
- wandb
- flash-attn (리눅스 전용)

전체 의존성 목록은 `pyproject.toml` 파일에서 찾을 수 있습니다.

## 선택적 의존성 설치

MGM(Multi-Modal Molecular) 모델로 작업하려면 추가 의존성을 설치할 수 있습니다:

```bash
pip install -e ".[mgm]"
```

이렇게 하면 분자 처리에 필요한 RDKit 및 SELFIES와 같은 추가 패키지가 설치됩니다.

## 설치 확인

다음 파이썬 코드를 실행하여 설치를 확인할 수 있습니다:

```python
from lobster.model import LobsterPMLM

# 사전 훈련된 모델 로드
model = LobsterPMLM("asalam91/lobster_24M")
print(f"모델이 {sum(p.numel() for p in model.parameters())}개의 파라미터로 성공적으로 로드되었습니다")
```

## GPU 지원

LBSTER는 GPU 가속을 통해 가장 잘 작동합니다. 패키지는 사용 가능한 경우 자동으로 CUDA를 사용합니다. GPU가 인식되는지 확인하려면:

```python
import torch
print(f"CUDA 사용 가능: {torch.cuda.is_available()}")
if torch.cuda.is_available():
    print(f"CUDA 장치: {torch.cuda.get_device_name(0)}")
```

## 문제 해결

설치 중 문제가 발생하면:

1. 올바른 파이썬 버전(3.10 이상)이 있는지 확인하십시오.
2. GPU 지원을 위해 호환되는 CUDA 버전이 설치되어 있는지 확인하십시오.
3. 전체 설치에 충분한 디스크 공간(~4GB)이 있는지 확인하십시오.
4. `flash-attn`에 문제가 발생하면 플랫폼에 따라 다를 수 있습니다. 패키지는 이 없이도 작동하지만 일부 작업에서는 속도가 느려집니다.

여전히 문제가 있는 경우 [GitHub 저장소](https://github.com/prescient-design/lobster/issues)에 문제를 제기하십시오.
