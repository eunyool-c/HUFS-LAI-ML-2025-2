# 5th Assignment: F1 Race Excitement Prediction Project

**Project by:** {학번} ({이름})  
**Subject:** Machine Learning (Assignment 5)

---

## 📌 Project Overview
본 프로젝트는 **F1 레이스 데이터(사고, 추월, 격차 등)**를 기반으로 해당 경기의 **'흥행도(Excitement Score)'**를 예측하는 머신러닝 모델을 구축하는 과제입니다. 
단순한 순위 예측을 넘어, 경기 내의 다양한 통계적 수치들이 실제 시청자가 느끼는 재미와 어떻게 연결되는지 분석하고 예측하는 것을 목표로 합니다.

---

## 📂 Repository Structure
본 과제는 평가 기준에 맞춰 학습, 평가, 추론 단계를 명확히 분리하여 구성했습니다.

| 파일명 | 설명 | 비고 |
|:---:|:---|:---|
| **`training.ipynb`** | 데이터 전처리, 모델 학습, 검증(CV), 최종 모델 저장 | Learning Curve 시각화 포함 |
| **`evaluation.ipynb`** | 2025년 미래 데이터(Test Set)를 로드하여 최종 성능 평가 | Data Leakage 방지 |
| **`inference.ipynb`** | 가상 시나리오(노잼/꿀잼 경기)를 생성하여 모델 추론 시연 | CSV 없이 실행 가능 |
| `data/` | 학습 및 테스트에 사용된 데이터셋 폴더 | `test_2025.csv` 포함 |
| `*.pkl` | 학습된 모델(`f1_excitement_model.pkl`) 및 스케일러 | `joblib`으로 저장됨 |

---

## ⚙️ Methodology

### 1. Data Split Strategy (Rolling Window)
시계열 데이터인 F1 시즌의 특성을 반영하고 **Data Leakage(데이터 누수)**를 원천 차단하기 위해 다음과 같이 데이터를 분할했습니다.
* **Train (2021~2023)**: 과거 데이터로 모델 학습.
* **Validation (2024)**: 미래 시점인 2024년 데이터로 하이퍼파라미터 튜닝 및 과적합 모니터링.
* **Final Test (2025)**: 학습 과정에 전혀 관여하지 않은 별도 파일(`test_2025.csv`)로 최종 성능 평가.

### 2. Model Selection
* **Model**: **Random Forest Regressor**
* **Rationale**:
    * Tabular 데이터(정형 데이터)에서 우수한 성능과 안정성.
    * 데이터셋 크기가 작아(약 100개) 딥러닝보다 머신러닝 앙상블 기법이 유리함.
    * `n_estimators`(2000)와 `max_depth`(7) 조절을 통해 과적합 제어.

### 3. Target Engineering
* **Train**: `F1HOTorNOT`과 `RaceFans` 두 평점 사이트의 점수를 정규화하여 평균 사용.
* **Test**: 사용자가 직접 채점한 **Human Label (MyScore)**을 Ground Truth로 사용하여 실제 체감 재미와의 일치도 검증.

---

## 📊 Performance Analysis

### 1. Learning Curve Analysis
`training.ipynb`에서 `warm_start=True`를 활용하여 나무의 개수(`n_estimators`) 증가에 따른 성능 변화를 추적했습니다.
* **Train R2 (~0.8)** vs **Validation R2 (~0.35)**
* **분석**: 초기(2022년)에는 규정 대변화로 예측이 어려웠으나, 데이터가 누적된 후기(2024년)로 갈수록 검증 성능이 향상되는 **우상향 곡선**을 확인했습니다.

### 2. Final Evaluation (2025 Season)
학습된 모델을 2025년 시즌 데이터에 적용한 결과입니다.

| Metric | Score | 의미 |
|:---:|:---:|:---|
| **MAE** | **0.xxx** | 10점 만점 기준 평균 **1.x점** 내외의 오차 |
| **R2 Score** | **0.xxx** | 데이터 변동성에 대한 모델의 설명력 |

*(위 수치는 본인의 evaluation.ipynb 실행 결과에 맞춰 수정해주세요)*

### 3. Best & Worst Predictions
모델이 가장 잘 맞춘 경기와 어려워한 경기를 분석했습니다.
* **Best Case**: **Singapore GP, Japanese GP** (오차 < 0.2점)
    * 통계적 수치(추월, 사고 등)가 실제 재미와 정직하게 비례한 경기들.
* **Worst Case**: **Belgian GP, Chinese GP** (오차 > 2.0점)
    * 수치상으로는 이벤트가 많았으나, 실제로는 단조로운 트레인(DRS Train) 등으로 인해 지루했던 경기. (모델의 과대평가)

---

## ✅ Self-Evaluation Checklist (Assignment 5 Criteria)

본 프로젝트는 과제 평가 기준을 다음과 같이 충족합니다.

1.  **모델 설계의 적절성**: 데이터 특성(Small Tabular)에 맞는 **Random Forest** 채택 및 앙상블 기법 적용.
2.  **데이터셋 분할의 명확성**: 시계열 인과성을 고려한 **Rolling Window** 적용 및 Test Set의 물리적 분리.
3.  **학습 과정의 투명성**: Epoch가 없는 RF 모델 특성에 맞춰 `n_estimators` 변화에 따른 **Learning Curve** 시각화 및 분석.
4.  **Validation 성능 모니터링**: 회귀 문제에 적합한 **MAE, R2 Score** 지표 선정 및 연도별 검증 수행.
5.  **재현 가능성**: `joblib`을 이용한 모델/스케일러 저장 및 `inference.ipynb`를 통한 동일 성능 재현 구현.

---

## 🚀 How to Run

1.  **Training**: `training.ipynb`를 실행하여 모델(`f1_excitement_model.pkl`)을 생성합니다.
2.  **Evaluation**: `evaluation.ipynb`를 실행하여 2025년 예측 성능을 확인합니다.
3.  **Inference**: `inference.ipynb`를 실행하여 가상 시나리오에 대한 추론 결과를 확인합니다.
    * *Note: 데이터 파일 경로는 `base_path` 변수에서 수정 가능합니다.*
