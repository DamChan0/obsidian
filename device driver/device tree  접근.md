
> paltform device 에서 device tree로 직접 접근하여 데이터를 입출력 할 수 있다, 
> 이떄 
> linux/of.h  , linux/of_device.h , linux/of_address.h 등의 헤더를 include 하여 사용 할 수 있다


# device tree의 정보

device tree에서 디바이스를 식별하기 위한 정보가 몇가지 있다. 

1. device name ( node name)
   디바이스 트리를 새로 만들때 @ 앞에 위치한 문자열이 이에 해당한다
```
i2c@7000c000 {
    ...
};
   
```

2. 호환성 문자열(compatible string):
   compatible 필드에 입력되는 값이다  /   compatible = "vendor,device", "vendor,family";

위 두개는 문자열을 비교하기위한 방법에서 사용되는 정보이다 


## 노드 찾기
위의 정보를 활용해서 노드를 찾는 함수는 다음과 같다

1. `struct device_node *of_find_node_by_name(struct device_node *from, const char *name);`
	- 설명**: <mark style="background: #D2B3FFA6;">주어진 이름</mark>을 가진 노드를 찾습니다.
	- 매개변수:
		- `from`: 검색을 시작할 노드. NULL일 경우 루트 노드부터 검색.
		- `name`: 찾을 노드의 이름.

2. `struct device_node *of_find_compatible_node(struct device_node *from, const char *type, const char *compatible);`
	- **설명**: <mark style="background: #D2B3FFA6;">주어진 호환성 문자열</mark>을 가진 노드를 찾습니다.
	- **매개변수**:
	    - `from`: 검색을 시작할 노드. NULL일 경우 루트 노드부터 검색.
	    - `type`: 찾을 노드의 타입. NULL일 경우 무시.
	    - `compatible`: 찾을 호환성 문자열.


## 속성읽기


크게는 변수 타입에 따라서 값 읽기와 클럭 값 읽기가 있다. 

```
/example_device@0 {
    compatible = "myvendor,mydevice";
    reg = <0x1000 0x100>; // 시작 주소 0x1000, 크기 0x100
    interrupts = <23>;    // IRQ 번호 23
};
```
### 변수 읽기

1. 정수 속성읽기
```c
int of_property_read_u32(const struct device_node *np, const char *propname, u32 *out_value)
```
- 노드에서 32비트 속성 값을 읽어온다
- propname : 읽을 속성의 key이름
- out_value : 읽어온 값을 저장할 포인터 (uint32_t)

위와 같은 구조로 String , bool, uint32_t array 값을 읽는 함수가 있다
- of_property_read_string
- of_property_read_bool 
- of_property_read_u32_array


### 리소스 매핑

struct resource 구조
```c
struct resource {
    resource_size_t start;    // 자원의 시작 주소
    resource_size_t end;      // 자원의 끝 주소
    const char *name;         // 자원의 이름
    unsigned long flags;      // 자원의 플래그 (메모리, I/O, IRQ 등)
    struct resource *parent, *sibling, *child; // 자원의 계층 구조
};
```

- 리소스 초기화 하기 => 커널이 디바이스를 사용하도록 하기 위한 작업으로 메모리 자원이나 I/O 포트를 매핑하고 사용할 준비를 하는 것이 포함된다.
	```c
	int of_address_to_resource(np, 0, &res);
	```

-   리소스 주소 예약 => 특정 메모리 주소를 예약하여 다른 하드웨어 드라이브나 프로세스가 사용하지 못하도록 하는 작업
	```C
	struct resource * request_mem_region(res.start, resource_size(&res), res.name)
	```

-  메모리 매핑 => 커널 가상 주소로 매핑하여 프로세스를 통해서 접근 할 수 있도록 한다.
	```C
	void __iomem * ioremap(res.start, resource_size(&res))
	```
-  자원 사용 => 초기화된 자원을 사용하여 하드웨어를 제어한다.
-  자원 해제 
	```C 
	void release_mem_region(resource_size_t start, resource_size_t n);
	```



## 속성 읽기 

디바이스 트리의 속성을 읽는 방식에는 get 타입 find 타입, read 타입이 있다. 
- 반면 read 타입의 경우 읽은 데이터를 저장할 포인터를 함수의 매개변수로 같이 전달하는 형태이다. 
### get
- get 타입의 경우 내가 찾은 값을 함수의 return 값으로 반환 하는 형태이다

```c
const void *of_get_property(const struct device_node *np, const char *name, int *lenp);
```
 - np : 속성 값을 가져올 디바이스 노드
 - name : 가져올 속성의 key값 이름
 - lenp : 속성의 길이를 저장할 포인터, NULL로 할 시 따로 저장하지 않는다

### find
- find 함수는 get처럼 결과 데이터를 반환하는 형태이다
- get과 다른점은 find는 속성하나의 값이 아니라 노드 자체를 찾는 용도이다

#### 특정 이름의 노드를 찾기 
```c
struct device_node *of_find_node_by_name(struct device_node *from, const char *name);
```

#### 특정 호환성 이름을 가진 노드 찾기
```c
struct device_node *of_find_compatible_node(struct device_node *from, const char *type, const char *compatible);
```

#### 특정 경로가 포함되어 있는 노드 찾기
```c
struct device_node *of_find_node_by_path(const char *path);
```
- of_find_node_by_path  예제 
```c
/dts-v1/;

/ {
    model = "Example Board";
    compatible = "example,board";

    soc {
        compatible = "example,soc";
        
        my_device: device@1000 {
            compatible = "example,mydevice";
            reg = <0x1000 0x100>;
        };
        
        another_device: device@2000 {
            compatible = "example,anotherdevice";
            reg = <0x2000 0x100>;
        };
    };
};

==============================================================================================================

#include <linux/module.h>
#include <linux/of.h>
#include <linux/of_address.h>

static int __init example_init(void)
{
    struct device_node *node;

    // 지정된 경로를 가진 노드를 찾기
    node = of_find_node_by_path("/soc/device@1000");
    if (node) {
        pr_info("Found node: %s\n", node->name);
    } else {
        pr_err("Node not found\n");
    }

    return 0;
}

static void __exit example_exit(void)
{
    pr_info("Example module exit\n");
}

module_init(example_init);
module_exit(example_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Author Name");
MODULE_DESCRIPTION("Example Module Using of_find_node_by_path");


```

 이때 말하는 path는 파일의 경로가 아니아 디바이스 트리안에서의 depth를 말한다. 

### read

- read는 get과 같이 노드의 데이터를 얻는 용도로 사용한다.
- get과 다른 점은 data를 반환한느 것이 아니라 저장할 포인터를 매개변수로 전달하는 프로토타입을 가지고 있다. 
- 또한 읽을 데이터의 타입이 함수에 따라 다르다

#### uint32_t 읽기
```c
int of_property_read_u32(const struct device_node *np, const char *propname, u32 *out_value);
```
- np : 속성 값을 가져올 디바이스 노드
-  name : 가져올 속성의 key값 이름
- out_value : 가져온 데이터를 저장할 포인터


#### string 읽기
```c
int of_property_read_string(const struct device_node *np, const char *propname, const char **out_string);
```

#### uint32_t array 읽기
```c
int of_property_read_u32_array(const struct device_node *np, const char *propname, u32 *out_values, size_t sz);
```
- sz : 읽을 배열의 크기 
- 예제 


### reg 접근

device tree의 속성중  reg에 대한 값을 읽어오는 방법

```c
int of_address_to_resource(struct device_node *dev, int index, struct resource *r);
```

- dev : target device node pointer
- index : reg 배열중 몇번 째 요소를 가져올 것인지 지정
	- reg는 <>로 되어있다 , 즉 u32 배열로 이루어져 있음
	- <start address, size , start address ,size .... > 의 형식으로 되어 있음
- r : resource 저장할 구조체 포인터

#### 여려개의 레지스터 쌍을 가진 경우 
```c
    struct resource res;

   for (i = 0; i < MAX_REG; i++) {  // 여러 개의 reg 속성 읽기 (3개의 메모리 영역)
        ret = of_address_to_resource(np, i, &res[i]);
        if (ret) {
            pr_err("Failed to get resource %d\n", i);
            continue;
        }

        pr_info("Resource %d: start=0x%lx, size=0x%lx\n", i, (unsigned long)res[i].start, (unsigned long)resource_size(&res[i]));
    }
```

r 인자에 struct resource res 배열의 각 요소를 \[i\]인덱스로 표시하여 반복하면서 각각 다른 주소에 데이터를 쓸 수 있도록 한다