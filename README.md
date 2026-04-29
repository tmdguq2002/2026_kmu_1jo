# 🏠 주택가격 적정가 예측 (Housing Price Prediction)

> Kaggle - House Prices: Advanced Regression Techniques  
> 머신러닝 기반 객관적 주택 적정가격 예측 모델 개발

---

## 📌 프로젝트 개요

주택 시장에서는 주관적 판단에 의존한 가격 평가로 인해 휴먼 에러 리스크가 발생합니다.  
본 프로젝트는 **주택 특성 데이터를 활용한 ML 기반 객관적 가격 예측 모델**을 개발하여, 가격 산정 및 투자 판단을 위한 보조 도구를 제공합니다.

> ⚠️ 본 모델의 결과는 가격 산정 및 투자 판단을 위한 보조/지원 도구이며, 실제 부동산 거래의 100% 자동 의사결정을 의미하지 않습니다.

---

## 🎯 성공 지표 (KPI)

| 단계 | KPI | 목표 |
|------|-----|------|
| 1차 (모델) | RMSLE | ≤ 0.13 (개인과제 0.15 대비 개선) |
| 2차 (피처) | 설명력 (R²) | ≥ 0.93 (개인과제 0.90 대비 개선) |
| 비즈니스 | Kaggle 리더보드 순위 | 상위 20% 진입 |

---

## 📂 데이터셋

- **출처**: [Kaggle - House Prices Competition](https://www.kaggle.com/c/house-prices-advanced-regression-techniques)
- **규모**: Train 1,460행 / 81개 변수

### 핵심 피처군

| 카테고리 | 변수 |
|----------|------|
| 공간 | `GrLivArea`, `TotalBsmtSF` |
| 입지 | `Neighborhood` |
| 품질 | `OverallQual` |
| 노후도 | `YearBuilt`, `YearRemodAdd` |

---

## 🔧 ML 접근법

### 전처리
- `SalePrice` 우측 편포 해결 및 RMSLE 최적화를 위한 **Log 변환** 적용
- 제출 시 `exp()` 역변환 적용

### 모델 진화 로드맵

```
Linear Regression (Baseline)
        ↓
   Random Forest
        ↓
LightGBM / XGBoost
        ↓
Stacking Ensemble
  ├── Base: RF · LightGBM · XGBoost
  └── Meta: Ridge (L2 정규화)
```

### 검증 전략
- **K-Fold Cross Validation** (k=5)
- 최종 모델 선정 기준: RMSLE

---

## ⚠️ 리스크 및 대응

| 리스크 | 영향 | 대응 방안 |
|--------|------|-----------|
| 결측값 비율 높은 변수 | 모델 성능 저하 | 단순 삭제 지양, 팀 논의 후 처리 방식 결정 |
| 범주형 변수 과다 (51개) 및 희소 클래스 | 과적합 / 차원 폭발 | 희소 클래스 병합, Ordinal 매핑, K-fold Target Encoding 선택적 적용 |
| `SalePrice` 우측 편포 | 고가 주택 예측 오류 확대 | 타깃 Log 변환 후 학습, 제출 시 역변환 |

---

## 🗓️ 마일스톤

| 주차 | 산출물 |
|------|--------|
| W8 (4/25) | EDA 리포트, 데이터 전처리 파이프라인 완성, 1-Pager 최종안 |
| W9 (5/2) | 베이스라인(선형회귀) + 피처 엔지니어링 + MLflow 트래킹 셋업 |
| W10 (5/9) | 최종 모델(앙상블) + 하이퍼파라미터 튜닝 + 프로젝트 발표 |

---

## 👥 팀 구성 및 역할

| 역할 | 핵심 책임 |
|------|-----------|
| 팀장 | 프로젝트 로드맵 관리, 조율, 최종 의사결정 |
| 데이터 리드 (2인) | EDA / 결측치 처리 / 데이터 품질 / 스키마 정리 |
| 모델링 리드 (2인) | 베이스라인 / 실험 설계 / 하이퍼파라미터 튜닝 |
| 시각화 리드 (2인) | 변수·모델 결과 시각화 / 발표 자료 / 기술 문서 |

- **의사결정 규칙**: 만장일치 지향 합의, 미합의 시 팀장 결정
- **정기 미팅**: 매주 화요일 19:00 (ZOOM)
- **비동기 채널**: KakaoTalk 단톡방 + GitHub

---

## 🛠️ 기술 스택

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-AA4A44?style=flat)
![LightGBM](https://img.shields.io/badge/LightGBM-02569B?style=flat)
![MLflow](https://img.shields.io/badge/MLflow-0194E2?style=flat&logo=mlflow&logoColor=white)

---


---

*작성일: 2026-04-29*
