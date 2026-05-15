# [cite_start]🏠 [KMU DS 2026] 1조 주택 가격 예측 프로젝트 최종 상세 보고서 [cite: 1]

## 📌 1. 프로젝트 개요 및 배경
[cite_start]본 프로젝트는 Kaggle의 "House Prices: Advanced Regression Techniques" 데이터셋을 활용하여 주택의 다양한 피처를 기반으로 최종 판매 가격을 예측하는 회귀 분석 모델을 개발하는 것을 목표로 합니다[cite: 3].
[cite_start]79개의 설명 변수(Explanatory Variables)를 통해 주거 공간의 거의 모든 측면을 분석하며, 데이터의 복잡성과 노이즈를 극복하기 위한 고도화된 머신러닝 기법이 요구됩니다[cite: 4].

### 🎯 프로젝트 목표 (KPI)
* [cite_start]**1차 목표**: 예측 오차의 척도인 RMSLE(Root Mean Squared Logarithmic Error)를 0.13 이하로 낮춤[cite: 6].
* [cite_start]**2차 목표**: 결정계수(R²)를 0.93 이상으로 끌어올려 모델의 설명력을 극대화함[cite: 6].
* [cite_start]**핵심 전략**: 데이터 정규성 확보, 도메인 지식 기반 피처 생성, 앙상블 학습을 통한 일반화 성능 강화[cite: 6].

---

## 🛠 2. 단계별 상세 수행 과정

### [cite_start]Step 1: 탐색적 데이터 분석(EDA) 및 타겟 변수 전처리 [cite: 8]
* [cite_start]데이터의 분포를 분석한 결과, 타겟 변수인 SalePrice는 우측으로 치우친(Right-skewed) 분포를 보였으며 왜도(Skewness)는 1.88에 달했습니다[cite: 9].
* [cite_start]이는 선형 모델의 전제 조건인 정규성을 위배하므로, `np.log1p` 변환을 적용하여 왜도를 0.12로 보정하였습니다[cite: 10].
* [cite_start]시각화 도구(Scatter Plot)를 통해 GrLivArea(지상 거주 면적)가 4,000sqft 이상임에도 가격이 매우 낮은 이상치(Outlier) 2건(Index 523, 1298)을 식별하였습니다[cite: 11].
* [cite_start]이러한 데이터는 모델에 편향된 정보를 제공할 수 있으므로 제거를 결정하였습니다[cite: 12].

### [cite_start]Step 2: 결측치 처리 및 데이터 정제 [cite: 13]
* [cite_start]결측치 처리는 단순히 평균값으로 대체하는 것이 아니라 변수의 의미에 따라 차등 적용하였습니다[cite: 14].
* [cite_start]**범주형 변수**: Garage, Basement, Pool 등 부대시설 관련 결측치는 해당 시설의 "부재"를 의미하는 "None"으로 명시적 대치하였습니다[cite: 15].
* [cite_start]**수치형 변수**: LotFrontage(도로 인접 거리)는 인근 동네(Neighborhood)의 특성이 반영되므로 동네별 중앙값으로 대치하였습니다[cite: 15]. [cite_start]그 외 면적 관련 결측치는 0으로 처리하였습니다[cite: 16].
* [cite_start]**품질 인코딩**: ExterQual, BsmtQual 등 품질 변수들은 TA(3), Gd(4), Ex(5)와 같이 수치적 순서가 의미를 가지므로 Ordinal Encoding을 적용하여 변수 간의 관계를 보존하였습니다[cite: 16].

### [cite_start]Step 3: 고급 피처 엔지니어링 및 통계적 변환 [cite: 17]
* [cite_start]모델의 성능을 획기적으로 높이기 위해 기존 변수들을 조합한 파생 변수를 생성하였습니다[cite: 18].
* [cite_start]**TotalSF**: 1stFlrSF + 2ndFlrSF + TotalBsmtSF를 합산하여 집의 실질적인 규모를 나타내는 통합 변수를 생성하였습니다[cite: 19].
* [cite_start]**QualSF**: TotalSF와 OverallQual을 곱하여 "크면서도 품질이 좋은 집"에 대한 가중치를 부여하였습니다[cite: 19].
* [cite_start]**Box-Cox 변환**: 75개 이상의 피처에 대해 왜도를 분석하고, 절대값 0.75 이상의 왜도를 가진 수치형 변수들에 대해 Scipy의 `boxcox1p` 변환을 적용하여 정규성을 극대화하였습니다[cite: 19].

### [cite_start]Step 4: 모델링 전략 및 MLflow 실험 관리 [cite: 20]
* [cite_start]단일 모델의 한계를 극복하기 위해 다중 모델 실험을 수행하였으며, 모든 과정은 MLflow SQLite 백엔드에 기록하여 성능 변화를 추적하였습니다[cite: 21].
* [cite_start]**개별 모델 최적화**: XGBoost와 LightGBM에 대해 `n_estimators`와 `learning_rate`를 세밀하게 조정하며 단일 모델 기준 R² 0.92 수준까지 확보하였습니다[cite: 22].
* [cite_start]**Stacking Ensemble**: Lasso, Ridge, GBR, XGB, LGBM을 Base Learner로 설정하고, 최종 예측치를 Ridge(Meta Learner)로 결합하여 각 모델의 강점을 결합하고 과적합을 방지하였습니다[cite: 22].

---

## [cite_start]📊 3. 최종 성과 분석 [cite: 23]

| 평가 지표 | 목표치 | 최종 달성치 | 비고 |
| :--- | :--- | :--- | :--- |
| **RMSLE** | 0.13 이하 | **0.1113** | [cite_start]달성 [cite: 24] |
| **R² (Price 기준)** | 0.93 이상 | **0.9374** | [cite_start]달성 [cite: 24] |
| **R² (Log 기준)** | 0.93 이상 | **0.9266** | [cite_start]근접 [cite: 24] |

* [cite_start]최종적으로 프로젝트 초기 목표였던 RMSLE 0.13을 압도적으로 달성하였으며, 실제 가격 기준 R² 역시 0.937을 기록하며 주택 가격의 93% 이상을 설명할 수 있는 모델을 구축하는 데 성공하였습니다[cite: 25].

---

## [cite_start]💡 4. 결론 및 향후 개선 방향 [cite: 26]
* [cite_start]**결론**: 본 프로젝트를 통해 정밀한 데이터 전처리와 도메인 지식 기반의 피처 생성이 단순한 모델 튜닝보다 훨씬 큰 성능 향상을 가져온다는 것을 확인하였습니다[cite: 27]. [cite_start]특히 Box-Cox 변환과 이상치 제거는 정규성을 높여 모델의 예측 신뢰도를 확보하는 데 결정적인 역할을 하였습니다[cite: 28].
* [cite_start]**향후 과제**: 향후 개선을 위해서는 앙상블 가중치를 Bayesian Optimization으로 더욱 세밀하게 최적화하거나, 외부 경제 지표(이자율, 지역별 발전 계획 등)를 추가하여 모델의 시계열적 예측력을 보강할 수 있을 것으로 기대됩니다[cite: 29].

---

## 👥 5. 프로젝트 팀 (Team 1)
* **팀장 (Leader)**: 권승협
* **팀원 (Members)**: 김창희, 장대로, 문일환, 이연준, 박강한, 황현호
