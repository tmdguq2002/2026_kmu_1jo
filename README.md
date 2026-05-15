## 📖 1. 프로젝트 개요 (Overview)
본 프로젝트는 **Kaggle의 "House Prices: Advanced Regression Techniques"** 데이터를 활용하여, 주택의 물리적 특성, 위치, 품질 등 79개의 다양한 변수를 분석하고 최적의 회귀 모델을 구축하여 판매 가격을 예측합니다.

단순한 모델링을 넘어 **데이터 정규성 확보, 도메인 기반 피처 생성, 그리고 최첨단 앙상블 기법**을 통해 예측 성능을 극대화하는 데 중점을 두었습니다.

### 🎯 핵심 목표 (KPI)
| 목표 지표 | Target | Description |
| :--- | :--- | :--- |
| **RMSLE** | **≤ 0.13** | 로그 스케일 기준 오차 최소화 |
| **R² Score** | **≥ 0.93** | 모델의 설명력 및 예측 신뢰도 확보 |
| **Strategy** | **Ensemble** | 개별 모델의 편향을 보정하는 Stacking 전략 채택 |

---

## 🛠 2. 주요 기술 및 프로세스 (Tech Stack & Pipeline)

### 🧩 Tech Stack
- **Languages**: Python
- **Libraries**: Pandas, NumPy, Scikit-learn, XGBoost, LightGBM, Scipy
- **Experiment Tracking**: MLflow (SQLite Backend)
- **Transformations**: Log Transform, Box-Cox, Ordinal Encoding

### 📈 Data Pipeline & Analysis
1.  **Exploratory Data Analysis (EDA)**
    - 타겟 변수(`SalePrice`)의 왜도(1.88) 확인 및 `log1p` 변환을 통한 정규화(0.12).
    - `GrLivArea` 기반 이상치(Outlier) 탐색 및 제거(Index 523, 1298).
2.  **Missing Value Imputation**
    - **범주형**: 부대시설 부재를 의미하는 "None" 명시적 대치.
    - **수치형**: `LotFrontage`를 인근 동네(`Neighborhood`) 중앙값으로 정밀 대치.
3.  **Feature Engineering (Advanced)**
    - **TotalSF**: 1층/2층/지하 면적을 합산하여 실제 거주 규모 피처 생성.
    - **QualSF**: 면적과 품질 지수를 결합한 가중치 피처 생성.
    - **Box-Cox**: 왜도 0.75 이상의 수치형 피처에 대해 정규성 극대화 변환 적용.

---

## 🤖 3. 모델링 전략 (Modeling Strategy)

단일 모델의 한계를 극복하기 위해 **Stacking Ensemble** 모델을 구축하였습니다.

- **Base Learners**: `Lasso`, `Ridge`, `Gradient Boosting Regressor`, `XGBoost`, `LightGBM`
- **Meta Learner**: `Ridge Regression` (과적합 방지 및 최적 가중치 결합)
- **Experiment Management**: MLflow를 통해 모든 하이퍼파라미터(`n_estimators`, `learning_rate` 등)와 메트릭을 추적하였습니다.

---

## 📊 4. 프로젝트 성과 (Results)

<div align="center">

| Metric | Target | Final Result | Status |
| :--- | :---: | :---: | :---: |
| **RMSLE** | 0.13 | **0.1113** | ✨ **달성** |
| **R² (Price)** | 0.93 | **0.9374** | ✨ **달성** |
| **R² (Log)** | 0.93 | **0.9266** | ✅ **근접** |

</div>

> **Insight**: 정밀한 전처리와 Box-Cox 변환이 모델 성능 향상에 결정적인 역할을 수행하였으며, 앙상블 학습을 통해 일반화 성능을 성공적으로 확보하였습니다.

---

## 💡 5. 향후 개선 방향
- **향후 계획** 요즘 바둑 세계에서는 누가 AI와 가장 비슷하게 두냐가 실력의 척도로 여겨지고 있다고 합니다. 그런것처럼 사람의 의견이 들어간 본 프로젝트와 사람의 의견이 일체 들어가지 않은 프로젝트를 수행하여 어느점이 다른지, AI마다 어떻게 접근하여 문제풀이를 하는지 비교하여 보는것도 좋은 공부가 될 것이라 생각됩니다.

---

## 👥 6. 팀 정보 (Team 1)
- **Project Leader**: 권승협 (Team Leader)
- **Members**: 김창희, 장대로, 문일환, 이연준, 박강한, 황현호
