# `lobster` 문서 🦞
이 저장소에는 [`lobster`](https.github.com/prescient-design/lobster)에 대한 문서가 포함된 `jupyter-book`이 있습니다.

### 문서 빌드하기
`uv`로 가상 환경을 만들거나 `mamba`로 conda 환경을 만듭니다. 다음으로 요구 사항을 설치합니다.
```bash
uv sync
``` 

또는 `requirements.in`에서 설치하고 `publish.sh`를 실행합니다.

노트북 출력을 렌더링하려면 [`lobster`](https://github.com/prescient-design/lobster)를 설치하십시오.

로컬에서 문서를 빌드하고 보려면
```bash
uv run jupyter-book build docs
open docs/_build/html/index.html
```

변경 사항이 준비되면 다음을 사용하여 게시합니다.
```bash
ghp-import -n -p -f docs/_build/html
```

### 문서 작성
팁: 문서로 작성 중인 `lobster` 코드베이스의 일부를 컨텍스트에 로드하고 프롬프트를 통해 마크다운 및 .py 파일을 생성합니다. 그런 다음 `jupytext`를 사용하여 코드에서 실행 가능한 ipynb로 변환합니다.
```bash
jupytext --to notebook myfile.py
```
VSCode에서 .venv를 커널로 선택합니다.

### 감사의 말
영감을 준 [Miguel González Duque](https.github.com/miguelgondu)와 `poli` [문서](https://machinelearninglifescience.github.io/poli-docs/)에 감사드립니다!