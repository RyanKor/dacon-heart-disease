# Dacon 심장 질환 예측 경진대회

![image](https://user-images.githubusercontent.com/40455392/147253889-07026c2a-b2fb-4920-bec7-d646000e0391.png)



## 1. 최종 스코어 (2021.12.24 마감일 기준)

- **0.91176 (52위, 상위 10%)**
- 22위부터 52위까지 동점자이기 때문에 실제 순위로 보면 10% 보다 더 안쪽이라고 볼 수 있음.

![image](https://user-images.githubusercontent.com/40455392/147253985-01c68697-b352-4b97-9db2-e0da7af8ca41.png)



## 2. 접근 전략

```
1. Base Code를 이용한 기본 구조 확인 (Logistic Regression)
2. Ridge, Lasso Regression을 이용해 정규화 값이 포함되었을 때 (각각 L2, L1 규제) 결과 값 확인하기.
3. XGBoost를 사용한 성능 확인하기
```

- 대회 시간이 많이 남지 않았던 만큼, 제출할 수 있는 횟수가 총 6번 밖에 없었습니다.

![image](https://user-images.githubusercontent.com/40455392/147263791-d5ba2f73-3c3a-4230-a692-66920d77637d.png)

- EDA에서 Heat Map으로 데이터의 칼럼별 상관관계를 파악 후, 가장 심장 질환 여부 (즉, target 칼럼)과 연관 없는 값들을 모델 훈련 시에 제외시켰습니다.

  ![image](https://user-images.githubusercontent.com/40455392/147263887-3959bb92-9d9b-4b28-bcbc-6443010f81f7.png)

- 위의 이미지에서 확인할 수 있는 것처럼, `restecg, chol, fbs, trestbps` 를 소거 후, 모델 훈련을 진행했더니 베이스 코드보다 약간 성능이 향상되는 것을 볼 수 있었습니다.
- 앞으로 칼럼별 값들의 상관관계를 위와 같이 확인해서 불필요한 값들은 소거시키고 사용할 수 있을 것 같습니다.
- 별다른 EDA 없이 상관 계수를 target 값을 기준으로 정렬해서 보여준다면, 이것만큼 훌륭한 EDA가 없지 않을까 싶습니다.



## 3. 프로젝트 구성

```
| - Readme.md
| - Dacon_final.ipynb
| - base_code
	| - [EDA]_데이터 분석 입문자를 위한 데이터 살펴보기.ipynb
	| - [Baseline]_1. 데이터 분석 입문자를 위한 데이터 분석 & 예측.ipynb
	| - [Baseline]_2. 단계적 회귀를 사용한 모델 성능 향상.ipynb
| - dataset
	| - train.csv
	| - test.csv
	| - sample_submission.csv

```



## 4. 각 모델별 제출 결과

| Model                                  | Hyper Parameter                                              | Score        |
| -------------------------------------- | ------------------------------------------------------------ | ------------ |
| Logistic Regression (점진적 개선 모델) | Default                                                      | 0.9117647059 |
| Baseline (베이스 코드)                 | Default                                                      | 0.8985507246 |
| XGBoost                                | Default                                                      | 0.7333333333 |
| XGBoost                                | params = {'max_depth' : 10,<br/>         'eta' : 0.1, <br/>         'objective' : 'binary:logistic',<br/>          "n_estimators" : 2000,<br/>         'eval_metric' : 'logloss',<br/>         'early_stoppings' : 100 } | 0.7333333333 |
| Ridge Regression                       | Default (Alpha = 0.5)                                        | 0.84375      |
| Ridge Regression                       | Alpha = 0.8                                                  | 0.84375      |

- 일 제출 제한이 3회로 되어 있기 때문에 대회 발견 후, 제출 할 수 있는 최대 횟수를 제출했습니다.
- Lasso 회귀 함수는 Accuracy & F1 Score가 검증 데이터에서 이미 Ridge & XGB보다 밀렸기 때문에 별도의 제출은 하지 않았습니다.



## 5. 모델별 Training & Validation 의 Accuracy & F1 점수 확인하기

- Test Data의 결과를 바로 제출하지 않았고, 모델별 검증 결과를 확인하고 제출했습니다.

### 5-1) 베이스 코드의 Accuracy & F1 점수

![image](https://user-images.githubusercontent.com/40455392/147264883-54af0167-15bd-4228-b3e5-d673d040d3bc.png)

- 프로젝트가 어려운 내용이 아니다보니 LogisticRegression만 잘 사용해도 좋은 성능을 얻는 것을 볼 수 있습니다.

### 5-2) 베이스 코드의 점진적 개선 전략에 따른 정확도가 향상된 Accuracy & F1 점수

![image](https://user-images.githubusercontent.com/40455392/147264625-377b0223-918e-4a8a-b34e-471af92d8b77.png)

- 한 가지 확인할 점은, 베이스코드보다 정확도는 떨어졌지만, 점수는 더 향상하는 결과를 가져왔습니다.

- 아마 모델 학습에 방해가 되는 요소들을 제고하고 학습시켰기 때문에 채점용 데이터에 보다 정밀한 점수를 끌어올리는데 기여하지 않았나 추측해봅니다.



### 5-3) Ridge Regression의 Accuracy & F1 점수

![image](https://user-images.githubusercontent.com/40455392/147265179-27908e85-9089-4e55-a8ca-da723a776925.png)

- 위의 이미지에서 보는 것처럼 모델 학습의 불필요한 값을 소거하고, L2 규제를 강화시켰더니 정확도와 F1 Score에서 Logistic Regression보다 높은 성능을 가져오는 것을 볼 수 있습니다.
- Ridge Regression 자체가 Logistic Regression에 L2 정규화를 적용한 모델이기 때문에 기존 베이스라인 코드를 개선한 값에 약간의 커스터마이징을 하면 더 높은 점수를 얻을 수 있을 것이라 판단했는데, 어느 정도 높은 점수이긴 하지만, 기존 코드보다 높은 점수를 얻지는 못했습니다.
- 예측 값의 threshold 값이 0.5일때보다 0.6일 때 정확도와 F1 점수 모두 높았고 이것이 곧 결과로 이어질 것이라 생각했는데, 제출 횟수가 적어 더 검증할 결과를 만들지 못하는 것이 아쉬웠습니다.



### 5-4) Lasso Regression Accuracy & F1 Score

![image](https://user-images.githubusercontent.com/40455392/147265688-acde76fb-d3c6-4d7f-9a48-6ad80ea0bbc9.png)

- Lasso Regression 함수는 L1 규제가 적용되어 있어서, 신경망에서 가중치 값이 0보다 작은 값들을 모조리 0으로 만들어서 규제하는 방식인데, 모델의 중요한 특성은 강조하는 관점에서 사용하면 좋겠지만, 현재의 경우 복수개의 칼럼을 학습에 사용하는 단계이고, 각 칼럼들의 특성이 엇비슷하게 중요하기 때문에 특출나게 강조해서 학습해야하는 부분이 존재하지 않습니다.
- 따라서 L2 규제가 적용되어 있던 Ridge 회귀 함수가 조금 더 성능이 좋았다고 볼 수 있겠습니다.



### 5-5) XGBoost Accuracy & F1 Score

![image](https://user-images.githubusercontent.com/40455392/147266447-083e16c3-ea8f-454d-aa22-da2ce849fc5b.png)

- 제출 횟수가 많이 없어서 XGB 기본 파라미터 설정으로 제출 했을 시점에는, 성능이 좋지 못했습니다.
- 게다가 Threshold가 0.5를 기준으로 볼 때, 정확도와 F1 점수 모두 앞서 언급한 3개의 회귀 모델보다 떨어졌습니다.

![image](https://user-images.githubusercontent.com/40455392/147266369-6fcbf7ee-1d36-418a-b96f-9859abd231a3.png)

- 하지만 threshold를 0.9까지 끌어올리니 성능이 좋은 것처럼 보였는데, 막상 이 모델을 가지고 테스트 데이터를 제출하니 점수는 기본 파라미터와 동일했습니다.
- 검증할 길이 없기는 하지만, XGBoost는 기본적으로 L1 & L2 규제를 모두 사용할 수 있어 성능이 탁월한데, 아마 하이퍼 파라미터를 충분히 조율해주면 Ridge 만큼 성능이 나오지 않을까 추측해봅니다.



## 6. 후기

- Heap map을 잘 사용하면, 모델 구성에 필요한 데이터와 불필요한 데이터를 한 번에 구분할 수 있습니다.

  - Column이 많다고 이제 쫄지 않을 수 있습니다.

- Accuracy & F-1 점수를 잘 비교하면서 모델 성능을 가늠하고, 테스트 데이터의 예측 점수를 제출하면, 제출 기회를 잘 활용할 수 있습니다.

- Train Data가 152개 밖에 안되서, 사실 프로젝트 참여에 전혀 관심이 없었는데 최근 MLops 강의를 들으면서 대규모 데이터 학습 전에 소규모 데이터로 학습을 미리 해보는 것이 중요하다는 얘기를 들은 게 생각나서 스피드런 느낌으로 빠르게 다양한 실험을 진행했습니다.

  - 소규모 데이터 학습이 중요한 이유는 다음과 같습니다.

    - 1. 대규모 데이터 학습 전에 오버피팅 되어 있는 모델로 테스트를 해봄으로써 모델 예측 값을 미리 확인할 수 있습니다.

    - 2. 버그 등 대규모 학습에서 발생하게 될 문제들을 파악할 수 있습니다.

    - 3. 리소스를 효율적으로 사용할 수 있습니다.

  - 때문에 쉽고 어려움을 떠나 데이터로 해 볼 수 있는 여러 관점들을 생각할 수 있을 것 같아 미니 실습 느낌으로 2 ~ 3시간 동안 몰입해 프로젝트를 해 볼 수 있었습니다.

- 딥러닝이 항상 정답은 아닙니다. 텐서플로우로 이 코드를 해결하는 것도 가능하겠지만, 자원 효율적이면서 충분한 성능을 끌어낼 수 있다면 머신러닝 단계에서도 좋은 모델을 구성하는 것은 가능합니다.