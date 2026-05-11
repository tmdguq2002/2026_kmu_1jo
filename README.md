# KMU DS 2026 Team 1 - House Price Prediction

Kaggle **House Prices - Advanced Regression Techniques** 데이터를 사용해 주택 가격을 예측한 프로젝트입니다.  
팀 1-Pager 양식의 `WHY / WHAT / WHO / HOW / WHEN / RISK` 구조에 맞춰 분석 노트북을 정리했고, MLflow로 실험 기록을 남겼습니다.

## Project Goal

주택 특성 데이터를 활용해 객관적인 주택 적정 가격 예측 모델을 개발합니다.

| 목표 | 기준 | 최종 결과 |
|---|---:|---:|
| RMSLE | `<= 0.13` | `0.109091` |
| R²(price) | `>= 0.93` | `0.937399` |
| R²(log) | `>= 0.93` | `0.924699` |

최종 모델은 **ElasticNet**입니다.  
RMSLE는 목표를 안정적으로 달성했고, 원 가격 기준 R²도 목표를 넘겼습니다. log 기준 R²는 목표에 근접했으나 소폭 미달입니다.

## Repository Structure

```text
.
├── 1pager.ipynb
├── 1pager_original_backup.ipynb
├── train.csv
├── test.csv
├── sample_submission.csv
├── submission_mlflow_elastic_net.csv
├── artifacts/
├── mlruns/
├── [KMU DS 2026] 팀 1-Pager 양식 (1조).docx
├── KMU_DS_2026_Team1_HousePrice_Process_Report.docx
├── KMU_DS_2026_Team1_HousePrice_Process_Report.md
└── README.md
```

| 파일/폴더 | 설명 |
|---|---|
| `1pager.ipynb` | 최종 분석 노트북 |
| `train.csv`, `test.csv` | Kaggle House Prices 데이터 |
| `submission_mlflow_elastic_net.csv` | 최종 제출 파일 |
| `mlruns/` | MLflow 실험 기록 |
| `artifacts/` | 그래프, feature importance 등 산출물 |
| `KMU_DS_2026_Team1_HousePrice_Process_Report.docx` | 개선 과정 보고서 |

## How To Run

1. 저장소 폴더로 이동합니다.

```powershell
cd C:\Users\kwon\Desktop\codex
```

2. Jupyter Notebook을 실행합니다.

```powershell
jupyter notebook
```

3. `1pager.ipynb`를 열고 위에서부터 순서대로 실행합니다.

노트북 앞부분에는 현재 커널에 필요한 패키지를 확인하고 설치하는 셀이 포함되어 있습니다.  
만약 `No module named mlflow` 오류가 나면 아래 명령을 노트북 셀에서 먼저 실행한 뒤 커널을 재시작하세요.

```python
%pip install mlflow
```

## MLflow

MLflow UI는 다음 명령으로 실행할 수 있습니다.

```powershell
mlflow ui --backend-store-uri .\mlruns
```

브라우저에서 아래 주소로 접속합니다.

```text
http://127.0.0.1:5000
```

실험 이름:

```text
KMU_DS_2026_Team1_HousePrice_1Pager
```

MLflow에는 다음 항목을 기록했습니다.

- 모델명
- 검증 방식
- 타깃 변환 방식
- feature 개수
- RMSLE
- RMSE
- R²(log)
- R²(price)
- CV 평균/표준편차
- 제출 파일
- 주요 그래프 및 artifact
- 최종 모델 pipeline

## Modeling Process

### 1. Initial Notebook Review

기존 `1pager.ipynb`는 SHAP, LIME, XGBoost 중심의 실습 코드가 섞여 있었습니다.  
팀 1-Pager 양식과 연결성이 약했기 때문에 먼저 노트북을 다음 구조로 재정리했습니다.

- WHY: 비즈니스 문제
- WHAT: 성공 지표
- WHO: 팀과 역할
- HOW: 데이터와 ML 접근법
- WHEN: 마일스톤
- RISK: 리스크와 가정

### 2. Initial MLflow Setup

초기에는 다음 모델을 비교했습니다.

- Ridge
- RandomForest
- GradientBoosting
- XGBoost

초기 결과는 다음과 같았습니다.

| 모델 | Hold-out RMSLE | Hold-out R²(log) | 해석 |
|---|---:|---:|---|
| Ridge | 약 `0.1220` | 약 `0.9117` | 기준 모델 |
| RandomForest | 약 `0.1433` | 약 `0.8782` | 성능 부족 |
| GradientBoosting | 약 `0.1177` | 약 `0.9178` | 초기 best |
| XGBoost | 약 `0.1178` | 약 `0.9177` | GradientBoosting과 유사 |

이 단계에서 RMSLE 목표는 달성했지만 R² 목표에는 부족했습니다.

### 3. Encoding And Environment Fixes

작업 중 두 가지 환경 문제가 있었습니다.

첫 번째는 노트북 한글 깨짐 문제였습니다.  
PowerShell 인코딩 문제로 markdown 셀의 한글이 실제로 `?`로 저장되었습니다. 이후 docx 원문에서 한글 내용을 다시 추출하고, 일부 문구는 유니코드 escape 방식으로 복구했습니다.

두 번째는 `No module named mlflow` 문제였습니다.  
터미널 Python에는 `mlflow`가 설치되어 있었지만, Jupyter 커널의 Python 환경이 달라 오류가 발생할 수 있었습니다. 이를 해결하기 위해 노트북 앞부분에 현재 커널 기준으로 패키지를 확인하고 설치하는 셀을 추가했습니다.

### 4. Missing Value Handling

처음에는 수치형 결측값을 median, 범주형 결측값을 most frequent로 처리했습니다.  
하지만 House Prices 데이터에서는 결측값이 단순 누락이 아니라 “시설 없음”을 의미하는 경우가 많습니다.

예를 들어:

- `GarageType` 결측은 차고가 없음을 의미할 수 있음
- `BsmtQual` 결측은 지하실이 없음을 의미할 수 있음
- `FireplaceQu` 결측은 벽난로가 없음을 의미할 수 있음

그래서 다음과 같이 개선했습니다.

| 컬럼 유형 | 기존 처리 | 개선 처리 |
|---|---|---|
| Garage 계열 | 최빈값 | `None` 또는 `0` |
| Basement 계열 | 최빈값 | `None` 또는 `0` |
| Fireplace 계열 | 최빈값 | `None` |
| Pool, Fence, Alley | 최빈값 | `None` |
| 면적/개수 계열 | median | `0` |

이렇게 처리하면 모델이 시설 부재 자체를 가격 예측 신호로 학습할 수 있습니다.

### 5. Feature Engineering

결측 처리 개선 후 집값 예측에 중요한 파생변수를 추가했습니다.

| 파생변수 | 의미 |
|---|---|
| `TotalSF` | 지하실 + 1층 + 2층 총면적 |
| `TotalFinishedSF` | 거주 면적 + 지하실 면적 |
| `TotalBath` | 전체 욕실 수 |
| `HouseAge` | 판매 연도 기준 주택 연식 |
| `RemodAge` | 판매 연도 기준 리모델링 후 경과 연수 |
| `HasGarage` | 차고 보유 여부 |
| `HasBasement` | 지하실 보유 여부 |
| `HasFireplace` | 벽난로 보유 여부 |
| `OverallQual_TotalSF` | 전체 품질 × 총면적 |
| `OverallQual_GrLivArea` | 전체 품질 × 지상 거주 면적 |

추가 개선 단계에서는 다음 변수도 넣었습니다.

| 추가 변수 | 의미 |
|---|---|
| `OverallGrade` | `OverallQual × OverallCond` |
| `ExterGrade` | 외관 품질 × 외관 상태 |
| `KitchenQual_OverallQual` | 주방 품질 × 전체 품질 |
| `GarageScore` | 차고 면적 × 차고 품질 |
| `BasementScore` | 지하실 면적 × 지하실 품질 |
| `BathPerRoom` | 방 대비 욕실 수 |
| `SFPerRoom` | 방당 생활 면적 |
| `*_log` variables | 주요 면적 변수의 log 변환 |

### 6. Final Model Selection

추가 파생변수를 넣은 뒤 트리 기반 모델보다 선형 규제 모델이 더 좋은 성능을 보였습니다.

최종 모델:

```text
ElasticNet(alpha=0.001, l1_ratio=0.5)
```

ElasticNet은 L1과 L2 정규화를 함께 사용합니다.

- L1: 불필요한 feature의 계수를 줄임
- L2: 계수가 과도하게 커지는 것을 방지
- one-hot 변수와 파생변수가 많은 상황에서 과적합을 억제

Target Encoding도 실험했지만 성능이 떨어져 최종 선택하지 않았습니다.

| 실험 | CV RMSLE | CV R²(log) | 판단 |
|---|---:|---:|---|
| ElasticNet + One-Hot | `0.109541` | `0.924167` | 우수 |
| ElasticNet stronger + One-Hot | `0.109091` | `0.924699` | 최종 선택 |
| Ridge + One-Hot | `0.110605` | `0.922759` | 양호 |
| ElasticNet + Target Encoding | `0.113169` | `0.918871` | 성능 하락 |
| XGBoost + Target Encoding | `0.116174` | `0.914805` | 성능 하락 |

## Final Results

| 지표 | 최종 값 | 목표 | 결과 |
|---|---:|---:|---|
| CV RMSLE | `0.109091` | `<= 0.13` | 달성 |
| CV R²(log) | `0.924699` | `>= 0.93` | 소폭 미달 |
| CV R²(price) | `0.937399` | `>= 0.93` | 달성 |
| Hold-out RMSLE | `0.109340` | `<= 0.13` | 달성 |
| Hold-out R²(log) | `0.929081` | `>= 0.93` | 거의 근접 |
| Hold-out R²(price) | `0.942459` | `>= 0.93` | 달성 |

## Key Takeaways

1. 단순히 모델을 복잡하게 만드는 것보다 데이터의 의미를 살리는 전처리가 더 중요했습니다.
2. 결측값을 최빈값으로 덮는 방식은 시설 부재 정보를 잃게 만들 수 있었습니다.
3. 집값 데이터에서는 면적, 품질, 연식, 시설 보유 여부의 조합 변수가 성능 개선에 크게 기여했습니다.
4. 파생변수가 충분히 만들어진 뒤에는 XGBoost보다 ElasticNet이 더 안정적인 성능을 보였습니다.
5. MLflow를 사용해 실험 기록을 남기면서 어떤 변경이 성능 개선에 도움이 됐는지 추적할 수 있었습니다.

## Submission

최종 제출 파일:

```text
submission_mlflow_elastic_net.csv
```

이 파일은 최종 ElasticNet 모델을 전체 학습 데이터로 학습한 뒤 `test.csv`에 대해 예측한 결과입니다.

## Next Steps

- Kaggle에 `submission_mlflow_elastic_net.csv` 제출 후 리더보드 점수 확인
- CV 점수와 리더보드 점수 차이 점검
- ElasticNet, Ridge, XGBoost 예측값 블렌딩 실험
- 고급 이상치 정책 추가 검토
- `Neighborhood` 등 위치 변수에 대한 추가 encoding 실험

