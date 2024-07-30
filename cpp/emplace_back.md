> [!tip]+ 
> Vector에 요소를 넣는 여러 가지 방법 중 
> 1. push_back
> 2. emplace_back 
> 이 두 가지를 비교해보자

## push_back
push_back 은 요소를 추가할 때 <mark style="background: #FFF3A3A6;">이미 만들어진</mark> 요소를 추가한다 
```cpp
#include <vector>
#include <string>
#include <iostream>

struct MyStruct {
    int number;
    std::string text;

    MyStruct(int num, std::string str) : number(num), text(str) {
        std::cout << "MyStruct constructor called!" << std::endl;
    }
};

int main() {
    std::vector<MyStruct> my_vector;

    MyStruct obj(1, "Hello");
    my_vector.push_back(obj); // 객체를 생성한 후 벡터에 추가 (복사 발생)

    for (const auto& elem : my_vector) {
        std::cout << "Number: " << elem.number << ", Text: " << elem.text << std::endl;
    }

    return 0;
}

```


## emplace_back
emplace_back은 요소를 <mark style="background: #FFF3A3A6;">새로 만들면서</mark> 추가한다
- 즉 만들 요소의 값을 매개변수로 하여 만드는 동시에 vector의 요소에 추가하게 된다
```cpp
#include <vector>
#include <string>
#include <iostream>

struct MyStruct {
    int number;
    std::string text;

    MyStruct(int num, std::string str) : number(num), text(str) {
        std::cout << "MyStruct constructor called!" << std::endl;
    }
};

int main() {
    std::vector<MyStruct> my_vector;

    my_vector.emplace_back(1, "Hello"); // 객체를 벡터 내부에서 직접 생성 (복사 없음)

    for (const auto& elem : my_vector) {
        std::cout << "Number: " << elem.number << ", Text: " << elem.text << std::endl;
    }

    return 0;
}

```


