# recom_sys
# AutoInt+ – Hyper-Parameter Tuning Log

추천 시스템 과제에서 **AutoInt+** 모델을 대상으로 수행한 실험 결과를 정리  
평가 지표는 **nDCG@10** 과 **Hit Rate@10** 을 사용했습니다.

---

## 1. Optimizer 탐색

> 공통 설정  
> `epochs=5 · learning_rate=0.0001 · dropout=0.4 · batch_size=2048 · embed_dim=16`

| 실험 | Optimizer | nDCG | Hit Rate |
| :--: | :------- | :---: | :------: |
| 1 차 | Adam | 0.66172 | 0.63026 |
| 2 차 | Adam&nbsp;+&nbsp;AMSGrad | 0.66164 | 0.63027 |
| 3 차 | AdamW | 0.66159 | 0.62996 |
| 4 차 | Adamax | 0.64647 | 0.62153 |
| **5 차** | **Nadam** | **0.66218** | **0.63056** |
| 6 차 | SGD | 0.55241 | 0.56786 |
| 7 차 | RMSprop | 0.54495 | 0.56434 |
| 8 차 | Adadelta | 0.56492 | 0.57420 |
| 9 차 | Adafactor | 0.58743 | 0.58552 |

**결론 → Nadam**  
가장 높은 nDCG·Hit Rate를 기록해 이후 실험의 기본 옵티마이저로 채택
Loss 함수는 이진 분류 특성상 **`BinaryCrossentropy`** 로 고정

---

## 2. Epoch 수 변화

| 실험 | Epochs | nDCG | Hit Rate |
| :--: | :---: | :---: | :------: |
| 10 차 | 10 | 0.66151 | 0.63036 |
| 11 차 | 20 | 0.66167 | 0.63023 |

> **Observation** 러닝레이트가 0.0001 로 너무 작아 epoch 수 증가가 성능에 큰 영향을 주지 못함.

---

## 3. Learning Rate 스윕 (Nadam 사용)

| 실험 | Learning Rate | nDCG | Hit Rate |
| :--: | :-----------: | :---: | :------: |
| 12 차 | 0.001 | 0.66255 | 0.63061 |
| **13 차** | **0.01** | **0.67102** | **0.63600** |
| 14 차 | 0.1 | 0.55717 | 0.57051 |

- **0.01** 에서 두 지표 모두 최고점을 기록.  
- 0.1 이상은 발산(성능 급락) → 과대 학습률.

---

## 4. 추가 Epoch 실험 (lr = 0.01)

| 실험 | Epochs | nDCG | Hit Rate |
| :--: | :---: | :---: | :------: |
| 15 차 | 10 | 0.67087 | 0.63054 |

- `lr = 0.01` 로 학습 시 **5 epoch 부근에서 검증 손실이 최저**로 나타났음.  
- 다음 시각화를 통해 과대적합 징후를 포착하게 됨
<img width="790" height="490" alt="시각화" src="https://github.com/user-attachments/assets/a671e87d-9f08-4cfb-b852-b19c0dbf006a" />
