## 매개변수 갱신

- `최적화(Optimization)`: 매개변수의 최적값을 찾는 문제

### `확률적 경사 하강법(SGD)`: 매개변수의 기울기를 구해 기울어진 방향으로 매개변수 값을 갱신하는 일을 반복해서 최적의 값에 다가감

- 단점 : **`비등방성(anisotropy) 함수`에서 탐색 경로가 비효율적**
  - 비등방성 함수 : 방향에따라 성질(여기서는 기울기)이 달라지는 함수
- SGD 단점 개선 : `모멘텀`, `AdaGrad`, `Adam`

### 모멘텀(Momentum)

- 속도: 기울기 방향으로 힘을 받아 물체가 가속된다는 물리법칙
- `αv`: 물체가 아무런 힘을 받지 않을 때 서서히 하강시킴

### AdaGrad

- `학습률 감소 (learning rate decray)`
  - 학습을 진행하면서 학습률을 점차 줄여가는 방법
  - 처음에는 크게 학습하다가 조금씩 작게 학습하는 것
  - 간단한 방법은 `매개변수 전체`의 학습률을 일괄적으로 낮추는 것
- **`AdaGrad`는 각각의 매개변수에 맞게 학습률을 낮춰줌**(adaptive)
- `h` : 기울기 값을 제곱하여 계속 더해줌
- 매개변수의 원소 중에서 많이 움직인(크게 갱신된) 원소는 학습률이 낮아짐 (학습률 감소가 매개변수의 원소마다 다르게 적용됨)
- 무한히 학습한다면 어느 순간 갱신량이 0이 됨
  - 이 문제 개선한 것이 `RMSProp`
    - `지수이동평균(EMA)`: 과거의 모든 기울기를 균일하게 더해가는 것(AddGrad)이 아니라 먼 과거의 기울기는 잊고 새로운 기울기 정보를 크게 반영함 (과거 기울기의 반영 규모를 기하급수적으로 감소시킴)

### Adam

- `Momentum` + `AdaGrad`
- 장점 : 하이퍼파라미터의 `편향 보정`이 진행

### 어떤 갱신 방법을 이용할 것인가?

- **풀어야 할 문제에 따라 다름**
  - 각자의 장단점이 있기 때문
- 하이퍼파라미터를 어떻게 설정하느냐에 따라도 결과가 달라짐

---

## 가중치의 초기값

- `가중치 감소(weight decay)`: 오버피팅 막고, general performance 높임
  - 가중치 매개변수의 값이 작아지도록 학습
  - 가중치 값을 작게 해서 오버피팅이 일어나지 않도록 함
- 가중치의 초기 값이 0이면 안 좋다.
  - Backpropagation에서 모든 가중치의 값이 똑같이 갱신되기 때문
  - 순전파에서 같은 값이 전달 -> 역전파 때 가중치가 모두 똑같이 갱신 됨 (곱셈 노드의 역전파를 떠올리면 이해 가능)
  - 따라서 초기값을 무작위로 설정해야함

### Hidden layer의 활성화값(활성화 함수의 출력 데이터) 분포

- 가중치의 초기값에 따라 Hidden layer의 활성화값들이 어떻게 변화하는지

1. W의 초기값 = 표준편차가 1인 정규분포
   - 각 층의 활성화 값들이 0과 1에 치우쳐 분포
   - 시그모이드 함수는 출력이 0(또는 1)에 가까워지면 미분이 0이 됨
   - 데이터가 0과 1에 치우쳐 분포하게 되면 역전파의 기울기 값이 점점 작아지다가 사라짐 => `기울기 소실(gradient vanishing)`
   - 기울기 소실은 딥러닝에서 심각한 문제
   - 참고) sigmod 미분 = `dL/dy * y * (y-1)`
2. W의 초기값 = 표준편차가 0.01인 정규분포
   - 0.5 부근에 분포 => 기울기 소실 X
   - 하지만 표현력 관점에서 큰 문제(`표현력 제한`)
     - 다수의 뉴런이 거의 같은 값을 출력하고 있으니 뉴런을 여러개 둔 의미가 없음
   - **적당히 고루 분포되는 것을 원한다!!**
     - 활성화값들의 분포가 고르게 분포되면 역전파 때 기울기 값들의 차이가 있을 것. 그럼 학습이 제대로 이루어지니까!
3. `Xavier 초기값`
   - 딥러닝 프레임워크들이 표준적으로 이용하는 W 초기값
   - 앞 계층의 노드가 n개면 표준편차가 1/root(n)인 분포를 초기값으로 사용하자!
     - 노드 개수가 많을수록 가중치의 분포가 좁게 퍼짐
   - 고르게 분포, 학습 효율적
   - 활성화 함수가 `선형`인 것을 전제로 함

### ReLU를 사용할 때 가중치 초기값

- `He 초기값`: ReLU에 특화된 초기값
- 노드가 n개일 때 표준편차가 root(2/n)인 정규분포 사용
  - ReLU는 음의 영역이 0이라서 더 넓게 분포시키기 위해 2배의 계수가 필요
- ReLU에 `Xavier` 쓰면 층이 깊어질수록 활성화값들의 치우침이 커지고 `기울기 소실`문제를 일으킴

### 참고

- **신경망에 아주 작은 (0에 가까운) 데이터가 흐른다 => 역전파 때 기울기가 작아진다. => 학습이 거의 이루어지지 않음**
- 현재는 ReLU 함수 쓸 때는 `He 초기값`, sigmoid나 tanh나 S자모양 곡선일 때는 `Xavier 초기값` 사용하는 것이 BEST
- 가중치의 초기값 설정은 아주 중요한 문제이다!

---

## 배치 정규화 (Batch Normalization)

- 각 층이 활성화를 적당히 퍼뜨리도록 **강제**
- 장점
  1.  학습을 빨리 진행할 수 있다. (학습 속도 개선)
  2.  초기값에 크게 의존하지 않는다. (초기값 선택 필요성 감소)
  3.  오버피팅 막는다. (드롭아웃 등 필요성 감소)
- 미니배치 단위로 정규화 (분포의 평균이 0, 표준편차 1이 되도록)
  - `Standard Normal Distribution`
- `배치 정규화 계층`마다 이 정규화된 데이터에 고유한 `확대`, `이동`, `변환` 수행
- 이 알고리즘은 순전파에서 씀

---

## 오버피팅

- 오버피팅이 일어나는 경우
  - 매개변수가 많고 표현력이 높은 모델
  - 훈련 데이터가 적은 경우

### 가중치 감소 (Weight decay)

- 오버피팅 억제용
- 학습 중 큰 가중치에 큰 **패널티**를 부과하여 오버피팅 막음
  - **원래 오버피팅은 가중치 값이 커서 발생하는 경우가 많기 때문**
- `Regularization` 이용
  - 가중치의 절대값을 가능한 작게 만드는 것 (가중치의 모든 원소를 0에 가깝게 함, 모든 특성이 출력에 주는 영향을 최소한으로 만드는 것)
  - `L2 norm`, `L1 norm`
    - L2 norm
      - 각 가중치 제곱의 합에 정규화 세기(Regularization Strength)를 조절하는 λ를 곱함
      - λ를 크게 하면 가중치가 더 많이 감소(Regularization 중요시), 작게하면 가중치 증가(Regularization 중요시 X)
- ex)
  - 순전파에서는 **1/2*λ*L2(W)** 을 손실함수에 더함
    - 1/2는 미분값 편하게 해주려고 넣음
    - λ는 정규화값 조정하는 하이퍼파라미터
  - 역전파에서 위의식 미분하면 λW
    - ex) W 갱신 시 SGD 이용하면 **W - lr*λ*W** 하면 되니까 간편
  - 기울기를 구하는 계산에 λW 더하면 된다.
  - 이를 통해 가중치가 갱신되는 가정에서 λ값에 따라 패널티가 적용됨
- 단, 신경망이 복잡하면 어려움

### 드롭아웃(Dropout)

- 복잡한 신경망에 사용 가능
- 뉴런을 임의로 삭제하면서 학습 (훈련 때 hidden layer의 뉴런을 무작위로 골라서 삭제함)
- 단, 시험 때는 모든 뉴런에 신호 전달하고, 각 뉴런의 출력에 훈련 때 삭제한 비율을 곱해서 출력함
- **앙상블 학습**
  - 개별적으로 학습시킨 여러 개의 모델의 출력을 평균내어 추론
  - 드롭아웃과 밀접함
    - 뉴런을 무작위로 삭제하는 행위 -> 다른 모델 학습 하는 것으로 해석
    - 추론 때는 뉴런의 삭제한 비율 곱함 -> 여러 모델의 평균
    - 드롭아웃은 앙상블 학습과 같은 효과를 하나의 네트워크로 구현했다고 보면 됨

---

## 적절한 하이퍼파라미터 값 찾기

- 각 층의 뉴런 수, 배치 크기, 학습률, 가중치 감소 계수 등

### 검증 데이터

- 하이퍼파라미터의 성능을 평가할 때 `시험 데이터`를 사용하면 안 된다!
  - 시험 데이터를 사용하여 하이퍼파라미터를 조정하면 하이퍼파라미터 값이 시험 데이터에 오버피팅 되기 때문
  - 하이퍼파라미터 전용 확인 데이터 (조정용 데이터) 필요 => 검증 데이터
  - `검증 데이터(validation data)`: 하이퍼파라미터 성능 평가
  - `훈련 데이터`: 매개변수 학습
  - `시험 데이터`: 신경망의 general 성능 평가

### 하이퍼파라미터 최적화

- 하이퍼파라미터의 `최적값`이 존재하는 범위를 조금씩 줄여감
  1.  우선 대략적인 범위 설정
      - 보통 `로그 스케일(log scale)`로 설정 : -1000 ~ 1000 등
  2.  그 범위에서 무작위로 하이퍼파라미터 값 골라냄(샘플링)
      - 이때 무작위탐색이 규칙탐색보다 좋다고 알려짐 (최종 정확도에 미치는 영향력이 하이퍼파라미터마다 다르기 때문)
  3.  2단계에서 샘플링한 하이퍼파라미터 값을 사용하여 학습하고 검증 데이터로 정확도 평가
  4.  정확도 보면서 2~3단계 반복해서 최적값의 범위를 줄여나감
- 참고) 더 세련된 최적화 방법 : `베이즈 최적화(bayesian optimizatino`)
  - 베이즈 정리 이용
