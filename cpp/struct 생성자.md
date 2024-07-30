
# Layer 
```cpp
struct Layer {
  Layer(const std::shared_ptr<Tensor> tensor, const std::string layer_name)
      : layer_name(layer_name), tensor(tensor) {}
  std::string layer_name;
  std::shared_ptr<Tensor> tensor;
};
using LayerIn = std::vector<Layer>;
using LayerOut = std::vector<Layer>;
```


