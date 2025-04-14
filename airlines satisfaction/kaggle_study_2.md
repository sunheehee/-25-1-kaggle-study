참고한 다른 팀원의 코드: https://github.com/Luna-luv/Kaggle_Study/blob/main/airplane%20passenger%20satisfaction/Airplane_Passenger_Satisfication.ipynb

---

# KNN

새로운 데이터가 주어졌을 때, 기존 데이터 중에서 가장 가까운 K개의 이웃의 정보로 새로운 데이터를 예측하는 방법

회귀문제의 경우, 종속변수의 평균값으로 예측
거리 측정방법에 따라 결과가 크게 달라짐 
유사도를 판단하는 기준으로 보통 유클리드 거리를 사용함

## 거리 계산 - 유클리드 거리
일반적으로 많이 사용되는 거리 계산 방법

두 점 사이의 직선거리 (최단거리) 를 측정하는 방법으로 자유롭게 이동하는 거리 계산에 유리함

두 점 사이의 거리는 피타고라스 정리를 이용해 계산된다. 

![alt text](<image/유클리드 거리.png>)
> 유클리드 거리 = 5

## KNN 작동 원리 예시

### 학습 데이터
![alt text](<image/학습 데이터.png>)

### 평가 데이터
![alt text](<image/평가데이터.png>)

### 결과
![alt text](<KNN 작동 결과.png>)

✔️ K=1이라면, 가장 가까운 값 1개의 특성으로 분류 → Red

✔️ K=5라면, 가까운 값 5개의 특성으로 분류하는데, 다수결의 원칙을 따라 Red로 분류 (Red(3), Green(2))

✔️ K=6이라면, Red(3), Green(3)으로 범주의 개수가 동일하게 나오는데, sklearn은 이와 같은 경우, 첫번째 레이블로 분류함.


## 하이퍼 파라미터 튜닝 (최적의 K값 찾기)

🌟KNN모델에서 K는 가장 중요한 하이퍼 파라미터

하지만, 이를 찾기 위해 사전에 정의된 통계 혹은 수학적 방법은 존재하지 않는다.

➡️테스트를 통해 가장 좋은 성능을 보이는 K값을 찾는 것이 최선

 - 반복문을 이용해서 1~10사이의 K값을 찾는 방법 (가장 일반적)
 - 하이퍼 파라미터를 찾아주는 Tunnel(GridSearch) 사용

```PYTHON
k_range = range(1,11)
train_scores=[]
test_scores=[]

for k in k_range:
    knn = KNeighbors(n_neighbors=k)
    knn.fit(X_train)
    train_scores.append(knn.score(X_train, y_train)
    test_scores.append(knn.score(X_test, y_test)
    
print(train_scores)
print(test_scores)
```

![alt text](<image/최적의 k값 찾기.png>)

🤷🏻최적의 K값은? 4,5,7,8
train score와 test score가 균형을 이루는 지점

➡️ test score가 안정적으로 유지되면서 train score와도 큰 차이가나지 않는 지점

---

![alt text](<image/최적의 k값 찾기2.png>)

🤷🏻최적의 K값은? 5
1. test정확도가 높으면서

2. train 정확도와 test 정확도의 차이가크지 않은 K값

 
 * K=6인 부분은 train데이터와 test데이터의 성능이 함께 낮아지는 구간 → 과소적합(Underfitting) 


# Confusion Matrix
모델의 분류 결과를 실제 값과 비교해서 무엇을 얼마나 맞췄고, 무엇을 틀렸는지를 정리해서 보여주는 표

### 🕵🏻‍♀️그냥 Accuracy만 확인하면 되는거아니야?
분류 문제에서는 단순 Accuracy만으로는 부족하다. **어디서 틀렸는지 구체적으로 파악할 수 있는** Confusion Matrix는 아주 중요한 역할을 한다.

## confusion matrix 기본 형태
![alt text](<image/confusion matrix.png>)

- TP: 정답도 1, 예측도 1 → 맞게 예측함 (긍정 잘 맞춤)

- TN: 정답도 0, 예측도 0 → 맞게 예측함 (부정 잘 맞춤)

- FP: 실제는 0인데 1로 예측 → 잘못된 긍정 (거짓 경보)

- FN: 실제는 1인데 0으로 예측 → 놓쳐버림 (감지 실패)

## 주요 평가 지표 계산식
![alt text](<image/주요 평가 지표 계산식.png>)

## 각 지표들의 활용

### 상황1) 특정값을 정확히 예측해야하는 경우(실패/성공)(암 yes/no)(스팸 yes/no) 

감지실패율을(FN)을 줄여야함

➡️ Recall 이 중요하다!

🌟 Recall은 **잡아내는 능력**

### 상황2) 이상값 감지 (EX: 금융 사기 감지)

잘못된 감지(FP)를 줄여야함

➡️ Precision이 중요하다!

🌟 Precision은 **정확히 찍는 능력**

✅ 둘의 균형은 F1 Score