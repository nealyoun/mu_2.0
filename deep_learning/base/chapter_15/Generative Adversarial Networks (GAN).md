# Generative Adversarial Networks (GAN)

### Generative Modeling

- GAN, generative adversalial networks
    - “적대적 생성기”라고도 불리었음
    - 데이터셋의 입력 데이터 없이 새로운 콘텐츠를 생성할 수 있는 신경망
- 생성 모델은 deterministic이 아닌 probabilistic이어야 함
- 생성 모델 ≠ GAN

### GAN Structure

- 생성기(Generator)와 판별기(Discriminator)로 구성
    - 생성기, 판별기 두 신경망을 경쟁적으로 학습시켜 둘의 성능을 함께 개선시키는 모델
- 처음에는 생성기와 판별기 모두 제대로 작동하지 않음

### Data Generate

- GAN 이전에는 오토인코더의 디코더 단이 Generative Model로 연구
- 하지만 오토인코더 자체는 입력데이터를 재현하는 것에 그침
- 그럼에도 인코더가 생성하던 코드 벡터를 난수 함수가 발생시킨 값으로 대체하여 디코더에 준다면 입력 없는 데이터를 생성
- 생성된 데이터의 품질을 향상시키기가 쉽지 않음

### Propagation & Back-Propagation

- 학습에 사용할 미니배치 원본 데이터를 데이터셋에서 로드
- 생성기의 순전파 과정을 실행하여 원본 데이터와 같은 수의 위조 데이터를 생성
- 판별기에는 원본 데이터에 1, 위조 데이터에 0을 매기고, 생성기는 위조 데이터에 1로 레이블링 하여 정답 벡터 구성
- 판별기의 순전파 과정으로 원본일 가능성을 로짓값 벡터로 얻음
- 로짓값 벡터와 정답 벡터 사이의 시그모이드 교차 엔트로피를 손실 함수로 구하고, 로짓값 벡터 성분들에 대한 손실 기울기를 구함
- 손실 기울기로 역전파 수행

### Problem

- 학습과정에서 장기간 안정되지 못하고 loss가 요동치는 현상 – 장기적으로 심하게 출렁이는 것이 아닌 점진적으로 증가 or 감소하는 형태를 보여야 함
- Mode collapse
- Loss가 유용한 지표가 되지 못함
- 하이퍼파라미터가 너무 많음