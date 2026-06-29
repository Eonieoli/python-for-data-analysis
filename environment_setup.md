# 『파이썬 라이브러리를 활용한 데이터 분석』 — uv 환경 셋업 가이드

> 책(Wes McKinney, *Python for Data Analysis*)은 **conda/Miniconda**로 설명하지만,
> 이 폴더에서는 **uv**로 진행한다. 책의 conda 명령은 거의 1:1로 uv에 대응되므로
> 막히는 단원 없이 따라갈 수 있다. (이 스택은 wheel이 성숙해서 conda가 필요 없다.)
>
> 대상 OS: **Windows 11**(주). macOS/Linux 차이는 맨 아래 참조.
> 작업 폴더: `python-for-data-analysis/` (이 파일이 있는 폴더)

---

## 0. 한 번만 — uv 설치

이미 하네스 작업으로 uv가 깔려 있으면 **건너뛴다** (`uv --version`으로 확인).

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

설치 후 새 터미널을 열고 확인:

```powershell
uv --version
```

---

## 1. 프로젝트 초기화

이 폴더(`python-for-data-analysis/`) 안에서:

```powershell
uv python install 3.13      # 인터프리터 (uv가 직접 관리)
uv init --python 3.13 .     # pyproject.toml + .venv 골격 생성
```

> `uv init`이 만든 `hello.py` 같은 샘플 파일은 지워도 된다.

---

## 2. 책 스택 한 번에 설치

```powershell
uv add numpy "pandas[performance]" matplotlib jupyterlab ipython `
       scipy scikit-learn statsmodels seaborn patsy `
       openpyxl lxml beautifulsoup4 html5lib requests pyarrow sqlalchemy
```

| 묶음 | 패키지 | 책에서 쓰이는 곳 |
|---|---|---|
| 핵심 | numpy · pandas · matplotlib | 전 챕터 |
| 노트북 | jupyterlab · ipython | 실습 환경 |
| 통계/ML | scipy · scikit-learn · statsmodels · patsy | 모델링 장 |
| 시각화 | seaborn | 플로팅 장 |
| 데이터 I/O | openpyxl(Excel) · lxml·beautifulsoup4·html5lib(HTML 파싱) · requests(웹) · pyarrow(Parquet/Feather) · sqlalchemy(SQL) | 데이터 로딩 장 |

> `pandas[performance]`는 pyarrow·numexpr·bottleneck을 함께 깐다(열지향 I/O·속도). 따로 `pyarrow`를 적은 건 명시적 의존으로 두기 위함이며 중복돼도 무해하다.

---

## 3. Jupyter 커널 등록 (필수)

이걸 안 하면 노트북에서 `ModuleNotFoundError`가 난다 (uv venv 커널이 자동 등록되지 않음).

```powershell
uv add --dev ipykernel
uv run ipython kernel install --user --name=pyda --display-name "Python for Data Analysis"
```

---

## 4. 실행

노트북(`.ipynb`)을 여는 방법은 **두 가지 중 택1**이다. 둘 다 같은 `.venv` 커널을 쓰므로 결과는 똑같다.

### 방법 A — JupyterLab (브라우저)

```powershell
uv run jupyter lab
```

실행하면 브라우저가 열리고 거기서 작업한다.

### 방법 B — VS Code (브라우저 없이)

VS Code에서 `.ipynb`를 직접 열어 똑같이 작업할 수 있다. **이때는 `uv run jupyter lab`을 따로 실행할 필요가 없다** — VS Code의 Jupyter 확장이 커널을 알아서 띄운다.

1. 확장 설치(한 번만): **Python** + **Jupyter** (둘 다 Microsoft 제공)
2. 이 폴더를 VS Code로 연다
3. `.ipynb` 파일을 열거나 새로 만든다
4. **우상단 커널 선택** → 이 폴더의 `.venv`(또는 §3에서 등록한 **"Python for Data Analysis"**)를 고른다
5. 셀 실행은 `Shift+Enter` — JupyterLab과 동일

> 두 방법은 **양자택일**이지 같이 켜는 게 아니다. 핵심은 "어떤 커널(=어떤 `.venv`의 파이썬)을 쓰느냐"이고, 둘 다 똑같은 `.venv`를 가리킨다.

### 그냥 스크립트 실행

```powershell
uv run python 코드파일.py
```

> 핵심 규칙: **항상 `uv run`을 앞에 붙인다.** Windows에서 맨손 `python`은 엉뚱한 인터프리터를 잡는 함정이 있다.

> **📓 Jupyter Notebook vs JupyterLab?**
> 책은 "Jupyter Notebook"이라고 부르지만, 이는 **집필 시점 표현**일 뿐이다. 둘은 같은 노트북(`.ipynb`)·같은 커널을 쓰고 **UI만 다르다.** Notebook은 1세대(단일 노트북 화면, 현재 유지보수 모드)이고 JupyterLab은 차세대(파일 탐색기+분할 화면을 갖춘 IDE형, Jupyter 팀 주력)다. 책의 모든 예제는 JupyterLab에서 그대로 돌아가므로 더 최신인 **JupyterLab을 권장**한다. (옛 UI가 필요하면 `uv run jupyter notebook`.)

---

## 5. 책 명령 → uv 번역표

책에 conda가 나오면 이렇게 바꿔 읽으면 된다:

| 책 (conda) | 이 폴더 (uv) |
|---|---|
| `conda create -n pydata python=3.10` | `uv init --python 3.13 .` (폴더당 환경) |
| `conda activate pydata` | 필요 없음 — `uv run ...` 가 알아서 .venv 사용 |
| `conda install pandas` | `uv add pandas` |
| `conda install -c conda-forge X` | `uv add X` |
| `pip install X` | `uv add X` |
| `python script.py` | `uv run python script.py` |
| `jupyter lab` | `uv run jupyter lab` |

> 새 패키지가 책 중간에 나오면 그냥 `uv add <패키지>` 하면 된다.

---

## 6. ⚠️ 책과 동작이 다를 수 있는 점 (pandas 3.0)

이 셋업은 **최신 pandas 3.x**를 깐다. 책은 pandas 1.x~2.x 기준이라 **일부 동작이 다르다.** 당황하지 말 것:

1. **연쇄 할당(chained assignment)이 막힌다** — `df[mask]["col"] = 값` 은 원본을 안 바꾸고 `ChainedAssignmentError` 경고가 뜬다. → 항상 `df.loc[mask, "col"] = 값` 으로 쓴다. (책 예제 중 옛 방식이 보이면 `.loc`로 고쳐 따라 한다.)
2. **문자열 컬럼 기본 dtype이 `object` → 전용 `str`** 로 바뀜. `select_dtypes(include="object")` 결과가 책과 다를 수 있다.
3. **datetime 기본 해상도가 `ns` → 입력 해상도(µs 등).** `datetime64[ns]`가 아닐 수 있다.

> **책과 100% 똑같이 보고 싶다면** pandas만 2.x로 내려도 된다:
> `uv add "pandas[performance]>=2.2,<3"`
> 다만 지금은 pandas 3이 현재 표준이라, 위 3가지만 알고 **3.x 그대로 배우는 걸 권장**한다.

---

## 7. ⚠️ 한국어 Windows 인코딩 함정

책의 CSV/텍스트 읽기 예제를 인코딩 인자 없이 따라 하면, 한국어 Windows(기본 **cp949**)에서 모지바케나 `UnicodeDecodeError`가 난다.

- 매번 명시: `pd.read_csv("파일.csv", encoding="utf-8")`, `open("파일", encoding="utf-8")`
- 또는 한 방에 해결 — 실행할 때 UTF-8 강제:
  ```powershell
  $env:PYTHONUTF8 = "1"; uv run jupyter lab
  ```
  (영구 적용하려면 시스템 환경변수에 `PYTHONUTF8=1` 추가)

---

## 8. Git으로 관리하기 — `.gitignore`와 재현성

### 8-1. 큰 그림: 무엇을 커밋하고 무엇을 버리나

핵심 원칙은 **"재료(recipe)는 커밋하고, 요리 결과물(생성물)은 커밋하지 않는다."**
`uv.lock`만 있으면 똑같은 `.venv`를 언제든 다시 만들 수 있으므로, `.venv` 자체는 git에 넣을 필요가 없다.

| 파일/폴더 | git 커밋? | 이유 |
|---|---|---|
| `pyproject.toml` | ✅ 커밋 | 어떤 패키지가 필요한지 적은 **명세서**(직접 의존성) |
| `uv.lock` | ✅ 커밋 | 그 패키지들의 **정확한 버전·해시 전체**를 고정한 잠금 파일 → 재현성의 핵심 |
| `.python-version` | ✅ 커밋 | 이 프로젝트가 쓸 파이썬 버전(3.13)을 박아둠 |
| `README.md` / `.md` 문서 | ✅ 커밋 | 문서 |
| `.venv/` | ❌ 무시 | `uv sync`로 **언제든 재생성** 가능한 생성물. 용량 크고 OS·경로에 종속됨 |
| `__pycache__/`, `*.pyc` | ❌ 무시 | 파이썬이 만든 캐시 바이트코드. 자동 생성물 |
| `.ipynb_checkpoints/` | ❌ 무시 | Jupyter 자동 백업 |
| 데이터·출력물(`*.csv` 등) | 상황에 따라 | 책 예제 데이터는 보통 무시. 아래 메모 참조 |

> **왜 `.venv`를 커밋하면 안 되나?**
> 가상환경 안에는 컴파일된 바이너리와 **절대경로**(`C:\Users\Remote\...`)가 박혀 있어서, 다른 PC에 복사해도 동작하지 않는다. 게다가 수백 MB라 git이 무거워진다. 반면 `uv.lock`은 텍스트 몇 KB로 같은 환경을 **재조립**할 설계도다. → 무거운 결과물 대신 가벼운 설계도를 버전 관리한다.

### 8-2. `.gitignore` 작성

이 폴더에 `.gitignore` 파일을 만들고 아래 내용을 넣는다. **(이미 같이 만들어 두었다.)**

```gitignore
# uv가 재생성하는 가상환경 — 절대 커밋 안 함
.venv/

# 파이썬 캐시/컴파일 산출물
__pycache__/
*.py[cod]
*.egg-info/
.pytest_cache/

# Jupyter 자동 백업·체크포인트
.ipynb_checkpoints/

# OS·에디터 부산물
.DS_Store
Thumbs.db
.vscode/
.idea/

# (선택) 책 실습용 대용량 데이터 — 직접 받는 거라 보통 커밋 안 함
datasets/
examples/
```

> `.gitignore`에 적힌 패턴과 일치하는 파일은 git이 **추적 대상에서 제외**한다. 그래서 `git add .` 를 해도 `.venv`나 `__pycache__`가 딸려 들어가지 않는다.

### 8-3. 다른 컴퓨터에서 똑같은 환경 만들기

다른 PC(또는 클론 직후)에서 단 두 단계면 끝난다:

```powershell
git clone <저장소 주소>        # pyproject.toml + uv.lock + .python-version 가져옴
cd python-for-data-analysis
uv sync                        # uv.lock 그대로 .venv 재조립 (동일 버전·해시)
```

그다음은 평소처럼 `uv run jupyter lab` 으로 실행하면 된다.
(Jupyter 커널은 PC마다 한 번씩 §3을 다시 해주면 된다 — 커널 등록은 그 PC 사용자 계정에 기록되는 거라 git으로 안 따라온다.)

> **`uv sync` vs `uv add`의 차이**
> - `uv add 패키지` = 새 의존성을 **추가**하고 lock을 **갱신**(버전이 새로 정해질 수 있음).
> - `uv sync` = lock에 **이미 적힌 버전 그대로** 환경을 맞춘다. 새로 풀거나 올리지 않는다.
> 그래서 협업/재현에서는 받아온 사람이 `uv sync`만 하면 **만든 사람과 1비트도 다르지 않은** 환경이 된다.

### 8-4. 평소 작업 흐름 (요약)

```powershell
# 책 보다가 새 패키지가 필요해지면
uv add 새패키지              # pyproject.toml + uv.lock 둘 다 갱신됨

# 그리고 둘을 커밋 (lock을 빠뜨리면 재현성이 깨진다!)
git add pyproject.toml uv.lock
git commit -m "add 새패키지"
```

> **자주 하는 실수:** `pyproject.toml`만 커밋하고 `uv.lock`을 빼먹는 것.
> 그러면 다른 PC에서 `uv sync` 할 때 버전이 미묘하게 달라질 수 있다 → "내 PC에선 됐는데" 문제의 단골 원인. **둘은 항상 같이 커밋한다.**

---

## 빠른 체크리스트

```powershell
uv --version                                          # uv 있음?
uv run python -c "import pandas, numpy; print(pandas.__version__)"   # 스택 됨?
uv run jupyter lab                                    # 노트북 실행
```

---

## macOS / Linux 학습자 메모

- uv 설치만 다름: `curl -LsSf https://astral.sh/uv/install.sh | sh` (또는 `brew install uv`)
- 인코딩 함정 없음(기본 UTF-8) — 그래도 재현성 위해 예제는 `encoding="utf-8"` 권장
- 나머지 명령은 전부 동일
