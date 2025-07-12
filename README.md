# recom_sys
## 옵티마이저 변동 시

| 실험 | Optimizer     | Loss               | Epochs | Learning Rate | Dropout | Batch Size | Embed Dim | NDCG        | HitRate     |
| -- | ------------- | ------------------ | ------ | ------------- | ------- | ---------- | --------- | ----------- | ----------- |
| 1차 | Adam          | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.66172     | 0.63026     |
| 2차 | Adam +AMSGrad | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.66164     | 0.63027     |
| 3차 | AdamW         | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.66159     | 0.62996     |
| 3차 | Adamax        | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.64647     | 0.62153     |
| 5차 | Nadam         | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | **0.66218** | **0.63056** |
| 6차 | SGD           | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.55241     | 0.56786     |
| 7차 | RMSprop       | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.54495     | 0.56434     |
| 8차 | Adadelta      | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.56492     | 0.5742      |
| 9차 | Adafactor     | BinaryCrossentropy | 5      | 0.0001        | 0.4     | 2048       | 16        | 0.58743     | 0.58552     |
|    |               |                    |        |               |         |            |           |             |             |

* 같은 조건에서 가장 높은 NDCG, HitRate를 기록한 옵티마이저는 Nadam이므로 이 모델의 훈련에 가장 적합한 옵티마이저를 Nadam로 가정하고 하이퍼파라미터 조정
* 어차피 이 모델은 추천이 맞았는지, 틀렸는지 2가지 경우만 취급하므로 Loss 함수는 `BinaryCrossentropy` 로 고정

## Epoch만 단독 변환

| 실험  | Optimizer | Loss               | Epochs | Learning Rate | Dropout | Batch Size | Embed Dim | NDCG    | HitRate |
| --- | --------- | ------------------ | ------ | ------------- | ------- | ---------- | --------- | ------- | ------- |
| 10차 | Nadam     | BinaryCrossentropy | 10     | 0.0001        | 0.4     | 2048       | 16        | 0.66151 | 0.63036 |
| 11차 | Nadam     | BinaryCrossentropy | 20     | 0.0001        | 0.4     | 2048       | 16        | 0.66167 | 0.63023 |

* 성능에 큰 변화 없음

  * `learning_rate` 가 너무 작아 수를 늘려도 기울기 하강 정도가 너무 작은 것으로 추측

    * → 다시 5로 회귀

## learning\_rate만 단독 변환

| 실험  | Optimizer | Loss               | Epochs | Learning Rate | Dropout | Batch Size | Embed Dim | nDCG        | Hit Rate    |
| --- | --------- | ------------------ | ------ | ------------- | ------- | ---------- | --------- | ----------- | ----------- |
| 12차 | Nadam     | BinaryCrossentropy | 5      | 0.001         | 0.4     | 2 048      | 16        | **0.66255** | **0.63061** |
| 13차 | Nadam     | BinaryCrossentropy | 5      | 0.01          | 0.4     | 2 048      | 16        | **0.67102** | **0.63600** |
| 14차 | Nadam     | BinaryCrossentropy | 5      | 0.1           | 0.4     | 2 048      | 16        | **0.55717** | **0.57051** |

* 학습률(learning rate)이 0.01일 때(13차) 두 지표 모두 가장 높음.
* 학습률이 너무 크면(0.1) 성능 급락.

## leaning\_rate = 0.01 시

| 실험  | Optimizer | Loss               | Epochs | Learning Rate | Dropout | Batch Size | Embed Dim | nDCG        | Hit Rate    |
| --- | --------- | ------------------ | ------ | ------------- | ------- | ---------- | --------- | ----------- | ----------- |
| 15차 | Nadam     | BinaryCrossentropy | 10     | 0.01          | 0.4     | 2 048      | 16        | **0.67087** | **0.63054** |

- 본래는 `leaning_rate = 0.01` 시의 epoch 수 변화에 따른 추이를 보려는 목적
- 시각화 결과가 아래와 같이 나옴에 따라 과대 적합이 의심되어 계획 변경

<img width="790" height="490" alt="시각화" src="https://github.com/user-attachments/assets/50c22b8c-8104-4011-ae32-97ca3f1beae2" />

## EarlyStopping 적용

| 실험 | Optimizer | Loss | Epochs (Early-Stopping) | Learning Rate | Dropout | Batch Size | Embed Dim | nDCG | Hit Rate |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 16차 | Nadam | BinaryCrossentropy | 10 (정지 시점 6) | 0.01 | 0.4 | 2 048 | 16 | **0.67101** | **0.63615** |
- EarlyStopping을 이용해 향후 과대적합 추가 발생 가능성 억제

## l2_reg_embedding 조정 (1e-5 → 1e-4) 및 Embed Dim 조정

| 실험 | Optimizer | Loss | Epochs | Learning Rate | Dropout | Batch Size | Embed Dim | nDCG | Hit Rate |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 17차 | Nadam | BinaryCrossentropy | 10(정지 시점 6) | 0.01 | 0.4 | 2 048 | 32 | **0.67089** | **0.63522** |

## 결론

- 과대적합을 방지하면서 성능을 끌어올릴 수 있는 방법은 하이퍼파라미터 튜닝만으로는 한계가 있음
- 다음에 실험을 할 경우 더 고차원적인 모델 구성에 대한 학습을 한 뒤 다시 진행을 해야 할 것 같음
