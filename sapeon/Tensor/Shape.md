

> [!TIP] Title
> Tensor::Shape 는 Tensor의 구성을 알 수 있는 class이다 
> > [!info]+ shape
> > 모델이 기대하는 입력 데이터의 포맷을 지정하기도 한다


특이한 점은 
Tensor::Shape 에서 Tensor는 class이고 tensor class안에 shape class가 선언되어 있으며
Tensor의 멤버 변수로 shape가 존재한다. 
- 따라서 Tensor 객체를 만들면 Shape도 같이 생성된다

``` cpp
class Tensor
{
.........
	class Shape{
	.....
	};
  uint64_t id_ = 0;
  Shape shape_;
  DataType datatype_;
  int elembytes_;
  u_char* data_;

};
```


