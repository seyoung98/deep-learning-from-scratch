## 딥러닝

- 딥러닝은 층을 깊게 한 심층 신경망이다.

### 정확도 높이기

- 앙상블 학습, 학습률 감소(learning rate decray), 데이터 확장(data augmentation)
- Data augmentation
  - 입력이미지를 알고리즘을 동원해 인위적으로 확장
  - `회전`, `이동`, `crop`, `flip(좌우뒤집기)` 등 미세한 변화 주어서 이미지 개수 늘림
  - 데이터가 몇 개 없을 때 효과적

### 층을 깊게 하는 이유

1. 깊게 할 수록 신경망의 매개변수 수가 줄어든다.
   - 매개 변수 수를 줄여 **넓은 수용 영역(국소적인 공간 영역) 확보**
   - 활성화 함수를 합성곱 계층에 끼움 => 활성화 함수가 `비선형 힘`을 가하고 비선형 함수가 겹치면서 더 복잡한 것도 표현 가능
2. 학습의 효율성 증가
   - 적은 학습 데이터로 효율적으로 학습 가능
   - 학습해야 할 문제를 `계층적으로 분해` 가능
     - ex) 처음 층은 에지(edge) 학습에 전념(개가 등장하는 이미지보다 에지가 등장하는 이미지가 많고, 에지의 패턴은 개라는 패턴의 구조보다 훨씬 간단하기 때문)
   - 층을 깊이 함으로써 각 층이 학습해야할 문제를 `풀기 쉬운 단순한 문제`로 분해

---

## GoogLeNet

- 인셉션 구조
  - 가로방향에 `폭`이 있는 것
  - 크기가 다른 필터를 여러 개 적용 후 그 결과를 결합
- 인셉션 구조를 하나의 빌딩 블록(구성 요소)으로 사용하는 것
- 1x1 필터를 사용한 합성곱 계층을 많은 곳에서 사용
  - 채널 쪽으로 크기를 줄이는 것
  - 매개변수 제거와 고속 처리에 기여

## ResNet

- 마이크로소프트 팀이 개발
- `스킵 연결` 도입
  - 딥러닝에서 층이 너무 깊으면 오히려 성능 떨어지는 문제 해결
  - 층의 깊이에 비례해 성능을 향상시킬 수 있음
    - 역전파 때 스킵 연결이 `신호 감쇠`를 막아주기 때문
    - 역전파 때 상류의 기울기를 하류에 그대로 흘려보냄
    - 스킵 연결로 기울기가 작아지거나 지나치게 커질 걱정 없이 앞 층에 **의미 있는 기울기** 가 전해지리라 기대 가능
    - 층을 깊게 할수록 기울기가 작아지는 소실 문제를 스킵 연결이 해결함
  - 입력 데이터를 합성곱 계층을 건너뛰어 출력에 바로 더함

## 전이 학습(transfer learning)

- 이미 학습된 가중치를 다른 신경망에 복사(가중치 초기값으로 학습된 가중치 사용)하고 그 상태로 새로운 데이터셋으로 재학습(fine tuning) 수행
- 보유한 데이터셋이 적을 때 유용

---

## 딥러닝 고속화

- 합성곱 계층에서 이루어지는 연산을 어떻게 고속으로 효율적으로 하느냐

### GPU(Graphical Processing Uint) 사용

- 병렬 수치 연산 고속으로 처리 가능
- GPU는 대량 병렬 연산(큰 행렬의 내적 등)에 특화, CPU는 연속적인 복합 계산에 특화
- 엔디비아 GPU
  - GPU 컴퓨터 통합 개발 환경인 CUDA 사용

### 분산 학습

- 다수의 GPU와 기기로 계산을 분산
  - 분산 학습을 지원하는 딥러닝 프레임워크 이용
    - 구글의 텐서플로우와 마이크로 소프트의 CNTK
- 컴퓨터 사이의 통신, 데이터 동기화 등 문제 => 텐서플로우같은 프레임워크가 해결

### 연산 정밀도와 비트 줄이기

- 메모리 용량, 버스 대역폭 등 고려
  - 메모리 용량
    - 대량의 가중치 매개변수와 중간 데이터를 메모리에 저장해야 함
  - 버스 대역폭
    - GPU(or CPU)의 버스를 흐르는 데이터가 많아져서 한계를 넘으면 병목됨
- 따라서 네트워크로 주고받는 데이터의 비트 수를 최소화해야 함
  - 많은 비트를 사용할수록 계산 오차 줄어듬, 하지만 계산에 드는 메모리 비용과 버스 대역폭에 부담
  - 딥러닝은 높은 수치 정밀도(수치를 몇비트로 하느냐)는 중요 X
    - `견고성`: 신경망 입력에 노이즈가 조금 있더라도 출력 결과가 달라지지 않음
    - 이런 견고성 때문에 신경망에 흐르는 데이터를 퇴화시켜도 됨
  - 16비트 반정밀도(half-precision)만 사용해도 학습에 문제 없다고 알려짐

---

## 딥러닝 활용

### R-CNN(Regions with Convolutional Neural Network)

- 사물이 위치한 후보 영역을 추출하고, 추출한 각 영역에 CNN을 적용하여 클래스 분류
- 후보 영역 추출(컴퓨터 비전) + CNN

### Faster R-CNN

- 후보 영역 추출까지 CNN으로 처리

### 분할(Segmentation)

- 이미지를 픽셀 수준에서 분류
- 픽셀 단위로 객체마다 채색된 `supervised data`를 사용함
- 추론할 때 입력 이미지의 모든 픽셀을 분류함
- 지금까지 구현한 신경망은 분류를 이미지 전체 대상으로함, 이들을 픽셀 수준에 적용하는 것
  - 단순한 방법은 모든 픽셀을 각각 추론!
    - 픽셀 수만큼 forward 처리를 해야해서 시간 오래 걸림 (합성곱 연산에서 많은 영역을 쓸데없이 다시 계산해야 함)
  - `FCN` 도입

### FCN(Fully Convolutional Network)

- 단 한 번의 forward 처리로 모든 픽셀의 클래스를 분류함
- 합성곱 계층만으로 구성된 네트워크
- 일반적인 CNN이 완전연결 계층을 이용, **FCN은 같은 기능을 하는 합성곱 계층을 이용**
- CNN의 Fully-connected layer에서는 중간데이터의 다차원 형태를 1차원으로 변환함
- FCN은 다차원 형태를 유지한 채 마지막 출력까지 처리 가능함
- 마지막에 공간 크기를 확대하는 처리를 도입(입력 이미지와 같은 크기까지 한번에 확대)
  - 이중 선형 보간(bilinear interplolation)에 의한 선형 확대
    - FCN은 이 선형 확대를 역합성곱(deconvolution)으로 구현

### 사진 캡션 생성

- 컴퓨터 비전 + 자연어
- 딥러닝으로 사진 캡션을 생성하는 방법
  - `NIC(Neural Image Caption)` 모델이 대표적임
    - `RNN(Recurrent Neural Network)` 사용
    - CNN으로 사진에서 특징을 추출하고 그 특징을 RNN에 넘김
    - RNN은 CNN이 추출한 특징을 초기값으로 해서 텍스트를 `순환적`으로 생성

### RNN(Recurrent Neural Network)

- RNN은 순환적 관계를 갖는 신경망으로 자연어나 시계열 데이터 등 연속된 데이터를 다룰 때 많이 사용함
- 신경망의 순환적인 네트워크 구조를 말함
- 순환적인 구조로 인해 이전에 생성한 정보에 영향을 받음(과거의 정보를 기억함)

### 멀티모달 처리(multimodal processing)

- 사진이나 자연어와 같은 여러 정보를 조합하고 처리하는 것

---

## 딥러닝 미래

### 이미지 스타일(화풍) 변환

- 두 이미지를 입력해 새로운 그림을 생성하는 연구
- `콘텐츠 이미지` + `스타일 이미지(화풍)`
- 네트워크의 중간 이미지가 콘텐츠 이미지의 중간 데이터와 비슷해지도록 학습
- 스타일 이미지의 화풍을 흡수하기 위해 `스타일 행렬`이라는 개념 도입
  - 스타일 행렬의 오차를 줄이도록 학습

### 이미지 생성

- 아무런 입력 이미지 없이도 새로운 이미지를 그려냄
- `DCGAN(Deep Convolutional Generative Adversarial Network)`
  - `생성자(Generator)`와 `식별자(Discriminator)`로 불리는 2개의 신경망 사용
    - `생성자`: 진짜와 똑같은 이미지 생성
    - `식별자`: 생성자가 생성한 이미지인지 실제로 촬영된 이미지인지 판단
  - 생성자와 식별자를 겨루도록 학습해서 생성자는 더 정교한 가짜 이미지 기술을 학습, 식별자는 더 정확하게 판단하도록 학습
  - 둘의 능력을 부지런히 갈고 닦아야 하는 개념은 `GAN(Generative Adversarial Network)` 기술
  - 성장한 생성자는 최종적으로 진짜로 착각할 정도의 이미지를 그려냄
  - `unsupervised learning`: 레이블이 없는 데이터셋 사용함(이미지 집합만 주어짐)

### 자율 주행

- `SegNet`: CNN기반 신경망으로 주변 환경을 정확하게 인식해냄
  - 입력 이미지를 Segmentation함

### Deep Q-Network(강화학습)

- `강화학습(reinforcement learning)`: 사람이 시행착오를 겪으며 배우듯 컴퓨터도 시행착오 과정에서 `스스로 학습`하게 하려는 분야
  - `에이전트`가 환경에 맞게 `행동`을 선택하고, 그 행동에 의해서 `환경`이 변한다.
  - 환경이 변하면 에이전트는 어떤 `보상`을 얻음
  - **강화학습의 목적은 더 나은 보상을 받는 쪽으로 에이전트의 행동 지침을 바로잡는 것**
    - 단 보상은 정해진 것이 아니라 `예상 보상`이라는 점. 명확한 지표로부터 예상 보상을 정해야 함
- `Deep Q-Network(DQN)`
  - 딥러닝을 사용한 강화학습
  - `Q학습`이라는 강화학습 알고리즘 기반
    - 최적 행동 가치 함수로 최적인 행동을 정함
  - 이 Q학습을 딥러닝(CNN)으로 비슷하게 흉내내어 사용하는 것이 DQN이다.
  - ex) DQN 연구 - 비디오 게임 unsupervised learning
    - 영상 프레임을 입력해서 최종적으로 게임을 제어하는 움직임에 대하여 각 동작의 `가치`를 출력
    - 입력 데이터는 비디오게임의 영상뿐임
    - 게임마다 설정을 바꿀 필요가 없음. DQN에 영상만 보여주면 됨
