
- **N (Batch Size)**: 배치 크기
- **W (Width)**: 이미지의 너비
- **H (Height)**: 이미지의 높이
- **C (Channels)**: 채널 수

```cpp
class Tensor {
 public:
  enum class Format : int32_t { NONE, NCHW, NHWC, NWHC, BERT };
```


> Format 역시 Tensor의 enum Class 이고 메모리에서 어떻게 배치되는지를 정의한다 

- **NONE**:
    
    - **설명**: 형식이 지정되지 않았음을 나타냅니다. 기본값으로 사용되며, 형식이 명확하지 않을 때 사용됩니다.
- **NCHW**:
    
    - **설명**: 배치 크기(Batch size), 채널(Channel), 높이(Height), 너비(Width) 순으로 데이터를 배치하는 형식입니다.
    - **용도**: 주로 합성곱 신경망(Convolutional Neural Networks, CNNs)에서 사용됩니다.
    - **예시**: 이미지 데이터가 `NCHW` 형식으로 저장될 때, 각 이미지의 데이터가 (배치 크기, 채널, 높이, 너비) 순으로 배치됩니다.
- **NHWC**:
    
    - **설명**: 배치 크기(Batch size), 높이(Height), 너비(Width), 채널(Channel) 순으로 데이터를 배치하는 형식입니다.
    - **용도**: 주로 TensorFlow와 같은 프레임워크에서 사용됩니다.
    - **예시**: 이미지 데이터가 `NHWC` 형식으로 저장될 때, 각 이미지의 데이터가 (배치 크기, 높이, 너비, 채널) 순으로 배치됩니다.
- **NWHC**:
    
    - **설명**: 배치 크기(Batch size), 너비(Width), 높이(Height), 채널(Channel) 순으로 데이터를 배치하는 형식입니다.
    - **용도**: 특정 데이터 처리 및 최적화 시 사용될 수 있습니다.
    - **예시**: 이미지 데이터가 `NWHC` 형식으로 저장될 때, 각 이미지의 데이터가 (배치 크기, 너비, 높이, 채널) 순으로 배치됩니다.
- **BERT**:
    
    - **설명**: BERT(Bidirectional Encoder Representations from Transformers) 모델에서 사용하는 데이터 형식입니다.
    - **용도**: NLP(Natural Language Processing) 작업에서 사용되는 BERT 모델의 입력 데이터를 위한 형식입니다.
    - **예시**: 텍스트 데이터가 BERT 형식으로 저장될 때, 토큰 시퀀스와 관련된 데이터 구조를 따릅니다.
