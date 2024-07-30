> Initialize 함수의 대부분은 데이터를 준비하는 과정이다
> Tensor를 선언하고 사용하려는 모델에 맞는 데이터 구조를 객체에 입력해주고 
> 모델의 이름이나 경로 등 기초 정보를 입력해 주는 과정이다


# yoloInference
## setInputData()

input_tensor_format_의 초기값은 NHWC값이다

```cpp
YoloInferencer::YoloInferencer(std::string model_smp_file_path,
                               Tensor::Format input_tensor_format) noexcept
    : NeuralNetModel(model_smp_file_path, input_tensor_format) {}
void YoloInferencer::SetInputData() {
  Tensor::Shape input_shape = model_info_.inputs[0].shape;
  input_shape.Transpose(input_tensor_format_);
  //input_tensor_ = PrepareImage(input_file_path_, input_shape);
  logger_.LoggingTimestamp(
      [&] { input_tensor_ = PrepareImage(input_file_path_, input_shape); },
      "PrepareImage");
}
```
### setInputData()->PrepareImage()
```cpp ln=true
inline Tensor PrepareImage(const std::string& image_path,
                           const Tensor::Shape& shape) {
  switch (shape.GetFormat()) {
    case Tensor::Format::NCHW:
      return PrepareImage(image_path, shape[0], shape[3], shape[2]);
    case Tensor::Format::NHWC:
      return PrepareImage(image_path, shape[0], shape[2], shape[1]);
    case Tensor::Format::NWHC:
    case Tensor::Format::NONE:
      return PrepareImage(image_path, shape[0], shape[1], shape[2]);
    default:
      std::stringstream err_msg;
      err_msg << "Unsupported Format : " << shape.GetFormat();
      throw std::runtime_error(err_msg.str());
  }
}
```



# yolov4Inference

> [!note]+ 
> yoloinference 객체를 이용하여 기본적인 모델의 구조를 설정 하고 나서 yolov4Inference 객체에 다시 채워 넣는 과정이다
> 
> 

```cpp
  logger_.LoggingTimestamp(
      [&]
      {
        inputs.emplace_back(
            std::make_shared<sapeon::runtime::Tensor>(input_tensor_),
            model_info_.inputs[0].name);
      },
      "emplace_back");
  logger_.LoggingTimestamp(
      [&]
      {
        // context_ = runtime_handle_->CreateInferenceContext(
        //     inputs, Tensor::Format::NHWC);
        context_ = runtime_handle_->CreateInferenceContext(
            inputs, Tensor::Format::NWHC);
      },
      "CreateInferenceContext");
}
```





















