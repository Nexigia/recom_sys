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

* 본래는 `leaning_rate = 0.01` 시의 epoch 변화 추이를 보려고 하였으나 시각화 결과가 아래와 같이 나옴에 따라 계획 수정


