# Autoencoder & Encoder - Decoder (chapter 13 & 14)

Assign: Anonymous, Anonymous
Due Date: December 16, 2021
Status: Completed

오토인코더(autoencoder)는 입력을 재현(representation)하는 방식의 비지도학습으로 레이블 없이도 데이터의 분포 특성을 스스로 파악하는 신경망

e.g. 차원축소, 표현/특징학습, 준지도학습 등 

## 13.1 오토인코더 구조

![Untitled](https://user-images.githubusercontent.com/54128055/147835463-284af859-31ef-4ee5-b534-ab013d8990b4.png)

- 위 그림처럼 인코더, 디코더 두 부분으로 구성되며, 디코더의 출력은 인코더의 입력과 같은 형태를 보임
- 출력은 입력과 동일한 형태를 가질 뿐만 아니라 내용까지도 최대한 비슷하게 재현(representation)되어야 하며 입력과 출력 사이의 평균 제곱오차를 손실 함수로 삼아 학습
- 그림처럼 보틀넥 형태를 띄는 부분을 코드(code, latent variable, hidden representation)라고 한다
- 즉 입력 부분에서 인코딩(암호화)을 거쳐 latent space로 차원이 축소되고(정보의 축약) 축약된 정보를 재현, 디코딩(복호화)되어 출력된다. 
—> 입력을 저차원 잠재공간으로 인코딩한 후 디코딩하여 복원하는 네트워크
—> 원본 입력을 재구성하는 방법으로 학습 (단순 입력을 출력으로 복사하는 과정이 아닌)

- 코드 레이어 부분은 설계자가 마음대로 정할 수 있지만 보통 입력에 비해 작은 크기로 정하는 것이 보통
(입력과 같거나 크면 간단히 모든 입력 성분을 코드에 복사하고 다시 출력에 복사하는 방식으로 재현이 가능하므로!)
—> 압축 과정에서 입력 데이터들이 갖는 유용한 패턴을 포착하는 것이 포인트

## 13.2 지도학습과 비지도 학습

- 딥러닝 및 ML 분야에서 학습은 크게 지도학습, 비지도학습으로 나뉜다.
- 학습용 데이터를 모으는 과정에서 수집된 데이터에 레이블링 작업을 진행하면 많은 인력과 비용이 들기 때문에 지도학습은 매우 많은 비용이 필요한 학습 방식
- 오토인코더를 이용한 학습은 출력 값이 입력과 동일한 형태(입력 자체가 레이블 정보 역할)로 쓰이기 때문에 레이블링 작업이 따로 필요없는 비지도학습 
but 오토인코더 자체로는 별 쓸모가 없다. (비지도학습만으로는 의미 X)
—> 인코더 부분을 이용한 내부 코드부분을 보면 유용한 패턴이 압축되어 표현되어 있어 활용 가치가 높다.
- 이러한 잘학습된 인코더 부분을 활용하는 가장 대표적인 방법은 약간의 레이블링 데이터를 이용한 지도학습을 수행하는 방법 (준지도학습) —> 인코더가 만들어낸 코드 정보로 적은 양의 데이터만으로도 높은 품질의 학습 결과를 얻을 수 있음

## 13.3 잡음 제거용 오토인코더

- 오토인코더에도 잡음을 주입하는 정규화(regularization)을 넣어 응용된 학습을 할 수 있다. (과적합 방지)
- 아래 그림처럼 입력과 인코더 사이에 잡음주입기를 추가하는 방식인 구조 
—> 이 때 주의할 점은 디코더 출력에 대한 손실 함수 값은 변형된 입력이 아니라 잡음 주입 이전의 원본 입력과 비교하여 계산되어야만 오토인코더 부분이 변형된 입력에서 잡음을 제거해 원복 데이터로 회복 시키는 기능을 가지게 됨
e.g. 저화질 사진에 일부 고화질 데이터를 넣어서 출력 값과 비교
- 학습, 평가 시점에는 잡음 주입기를 사용하지만 실제용도로 사용되는 정규화 시점부터는 동작 중지한다.

![Untitled 1](https://user-images.githubusercontent.com/54128055/147835461-03031a1a-067b-4f08-8d6a-abfcf1291dcc.png)

## 13.4 유사 이진 코드 생성과 시맨틱 해싱

- 오토인코더가 활용될 수 있는 분야로 시맨틱 해싱을 이용한 정보검색 분야다.
시맨틱 해싱은 검색 대상 컨텐츠를 적당한 수의 비트 정보로 표현된 이진 백터 공간에 대응시켜 쉽고 빠르게 정보 검색을 하는 방법

![Untitled 2](https://user-images.githubusercontent.com/54128055/147835462-81bb5937-628d-476d-be8a-21ed966a6c09.png)

- 위 그림처럼 척추/무척추, 육상/해상, 육식/초식에 따라 세 가지 비트값이 부여될 때 (이진분류, 32비트면 2^32, 약 40억개)
척추동물(1xx), 육상동물(x1x), 육식동물(xx1)인 사자에 대한 콘텐츠는 111의 코드 값 부여
호랑이가 검색 키라면 시맨틱 분석을 통해 코드 값 111이 부여될 수 있음
- 검색 결과를 얻는 과정이 해싱 테이블을 통해 이루어져 마치 배열 원소를 꺼내듯 빠르게 처리가 가능
- 비트 수가 많아져 영역의 수가 콘텐츠 양에 비해 지나치게 많아지면 빈 영역이 많이 생겨 데이터 구조가 효율적으로 관리되도록 주의해야하며 정확히 매치되는 콘텐츠가 없을 때 유사 영역을 찾는 알고리즘도 필요하다.
그래서 '어떻게 적절한 이진 분류 기준을 잡고 어떻게 각각의 데이터에 이진 코드값을 찾아 부여할지' 가 매우 중요함
- 이러한 문제를 해결할 수 있는 방법이 오토인코더를 이용하여 인코더가 생산하는 내부 코드를 시맨틱 해싱을 위한 이진 코드값으로 이용하는 것!!
→ 내부 코드가 유용한 패턴을 포착 했을 가능성이 크기 때문에 같은, 비슷한 이진코드를 갖는 항목끼리는 중요한 공통점을 갖고 있기를 기대
- 이진분류를 위해 내부 코드는 유사 이진코드를 유도해야함
→ 인코더 최상단 계층의 시그모이드 함수를 이용 (유사 이진 코드로 학습되면 반올림)
- 해싱 키로썬 이진 코드가 편리하지만 유사 이진 코드도 의미는 있다. (유사도의 근거)

### PCA vs AE

PCA 선형변환, AE비선형 → 실생활에서는 AE에서는 비선형이 좀 더 유용할 수 있음 

## 13.5 지도학습이 추가된 확장 오토인코더

- 인코더 + 디코더 + 지도학습기로 구성되는 신경망
    - 오토인코더: 인코더 + 디코더
    - 학습된 인코더 + 특정 문제를 풀어내는 지도학습기
- 확장 오토인코더 모델 학습
    - 오토인코더 학습 단계
    - 지도 학습 단계

![Screen_Shot_2021-12-23_at_9 49 40_PM](https://user-images.githubusercontent.com/54128055/147835456-60ed8eb8-c0b8-45ad-b18e-0fd56d2a7746.png)

### 오토인코더 학습 단계

- 비지도 학습 방식으로 수행 (input data)
- Loss Function은 입력과 출력 사이의 MSE
- 인코더와 디코더 모두 학습의 대상
    - 지도학습기는 해당 학습 과정에서 무관

### 지도 학습 단계

- 입력 데이터와 정답 레이블을 가지고 응용 분야의 문제를 풀어나가는 방식(input data + label)
- 학습된 인코더를 이용해 지도학습기 학습
- 디코더는 지도학습 단계에는 전혀 이용되지 않음
    - 반면, 지도 학습 과정 중 인코더의 파라미터가 변함에 따라 오토인코딩 기능이 손상 될 수 있음
        - 따라서 인코더를 순전파에만 이용하고 역전파 처리에서는 배제시켜 학습시키지 않는 옵션을 두는 것 또한 한가지 방법

---

# Encoder-Decoder

- Encoder-Decoder 는 두 부분이 직렬로 연결된 신경망 구조
    - 자연어 처리에 특히 유용한 구조
- 인코더: 입력 데이터를 분석하여 해당 데이터를 "context vector" 형태로 추출
- 디코더: 추출된 context vector로부터 원하는 형태의 출력을 생성
    - context vector
        - 인코더의 출력이자 디코더의 입력
        - 입출력에 비해 작고 간단한 형태로 설계
        - 입력과 전혀 다른 형태의 출력을 생성
        - 정보의 압축 효과를 기대
- 입력 형태와 출력 형태가 서로 독립, 즉 각각 어떤 형태든 무관

![Screen_Shot_2021-12-23_at_11 11 11_PM](https://user-images.githubusercontent.com/54128055/147835459-c6d82cfc-6884-44f3-ba77-8a64757fdf27.png)

### 인코더-디코더의 활용

- 입력 형태와 출력 형태가 상이한 분야
    - 시각 데이터 → 언어 데이터: 이미지의 내용을 설명하는 문구 생성
    - 언어 데이터 → 음원 데이터: 텍스트 내용을 음성 합성으로 음원 생성
- 입출력 모두 시계열 데이터이지만 입출력 사이의 시간대 의미가 서로 다른 경우
    - 번역기 (입출력 시계열 데이터의 시간대 사이에 정확한 일대일 대응 관계가 아닌 경우)
    - 시계열 데이터를 분석 → 핵심 정보를 context vector로 요약 → 디코더로 유용한 형태의 출력 생성

### 인코더-디코더 & 오토인코더

- 인코더와 디코더가 직렬로 연결되는 점은 유사
- 오토인코더의 구조는 정답 정보가 따로 필요 없는 비지도 학습 방식의 신경망
- 인코더-디코더는 입력 형태와 무관한 출력을 생성 및 출력을 정답 정보와 비교하며 학습하는 지도 학습

### 인코더-디코더 언어 처리

- 입출력 언어 사이의 길이나 어순 및 각종 표현 상의 차이를 극복 가능
    - 입력 시간대에 나타나는 알파벳 혹은 단어에 대해 일대일 대응 혹은 그에 준하는 관계를 이용해 출력을 생성하면 번역이 불가능
- 인간이 문장 혹은 문단 정도는 읽고 내용을 번역하는 것 처럼, 머릿속에 정리된 문장 혹은 문단이 "context vector"
    - context vector는 신경망 학습 결과에 따라 달라지므로 정확한 내용은 파악할 수 없음
    - 인간이 떠올리는 핵심 개념과 유사한 내용이 포착되기를 기대
- Attention: 인코더의 시간대별 출력 가운데 주목할 부분을 추적하는 학습 기법
- NLP 신경망은 인코더-디코더 구조 내부에 임베딩과 어텐션을 추가하여 구현
    - 임베딩 기법 및 학습 기법: CBOW, Skip-Gram, Word2Vec, BERT

### 인코더-디코더 분리 학습 문제

context vector에 추출되어야 할 정보가 어떤 내용을 갖는 것이 좋을지 정할 수 있음

- 특별한 개입이 없다면 신경망이 자체적으로 학습 과정을 통해 결정
- 사람이 context vector의 형태를 정하여 학습이 보다 용이하게 이루어지도록 설정
    - 숫자 이미지를 영어로 읽기: 이미지가 나타내는 숫자 종류
    - 10 이상의 숫자 이미지를 한글로 읽기: 연속된 장의 이미지가 나타내는 숫자값

**BUT,** 적당한 내용의 context vector를 지정해주는 것이 학습에 항상 유리한 것은 아님

- 인코더와 디코더를 따로 학습시키는 경우가 더 나은 결과를 가져올 수도 있음
    - 인코더: input → context vector 과정만 학습
    - 디코더: context vector → output 과정만 학습
- 데이터, 풀고자 하는 문제마다 다르다고 생각됨
    - context vector에 개입 없이 인코더-디코더 전체를 학습, end-to-end
    - context vector를 매개로 인코더와 디코더를 완전히 분리시켜 따로 학습
    - context vector를 매개로 분리시켜 학습 후, 인코더-디코더 전체를 재학습