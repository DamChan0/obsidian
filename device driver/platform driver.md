
> [!info]+ What
> Platform device에 대한 드라이버로 soc안에 내장된 동적으로 등록되지 않는 디바이스에 대한 드라이버이다

# device driver와 차이점

- device driver와 달리 ko 파일의 형태의 결과물이 나와서 모듈 형태로 사용하는 것이 아니다
  
- device driver에는 없는 probe 함수가 있다
  
- .o 파일의 결과물을 도출하고 커널이 초기화 될때 device tree에 platform driver에서 찾는 이름과 같은 이름의 하드웨어가 있다면  활성화되는 방식이다
	- 활성화 = probe 함수가 동작한다.

# Platform driver 추가하기

## 1. device tree 

- device tree에 사용하고자하는 디바이스에 대한 정보를 추가해야한다
```c
/ {
	aliases: aliases {
		test_drv = &test_drv;
	};
	
	test_drv: test_drv@11000000 {
		compatible = "telechips,tdrv";
		reg = <0x0 0x11000000 0x0 0x200>;
		interrupts = <GIC_SPI 146 IRQ_TYPE_LEVEL_HIGH>;
		clocks = <&fbus_test FBUS_CA53>;
		clock-names = "testbus_clk";
		status = "okay";
		cpuclk-frequency = <933333333>;
	};
	
	reserved_memory: reserved-memory {
		pmap_test_drv: test_drv {
			alloc-ranges = <0x0 RSVD_MEM_BASE_32 0x0 RSVD_MEM_SIZE_32>;
			status = "disabled";
		};
	};
};

```  

해당 파일을 dtsi 파일로 위 파일을 include 해야하는 dts 파일을 찾아서 추가해줘야한다

## 2. platform driver 작성 필수 요소들 

## 필수 요소들 
### 기본 struct

```c
struct platform_driver {
	int (*probe)(struct platform_device *);
	int (*remove)(struct platform_device *);
	void (*shutdown)(struct platform_device *);
	int (*suspend)(struct platform_device *, pm_message_t state);
	int (*resume)(struct platform_device *);
	struct device_driver driver;
	const struct platform_device_id *id_table;
	bool prevent_deferred_probe;
};
```
### \_init\_ , \_exit\_ 함수 

```c
static int32_t __init test_init(void)
{
	int ret = class_register(&test_class);
//,,,,
	ret = platform_driver_register(&test_driver);
	return 0;
}

static void __exit test_exit(void)
{
	platform_driver_unregister(&test_driver);
	device_destroy(&test_class, test_devs.cdev.dev);
	cdev_del(&test_devs.cdev);
	class_unregister(&test_class);
}

module_init(test_init);
module_exit(test_exit);
```

### probe 함수 
```c
	static int32_t test_probe(struct platform_device *pdev)
{
	int32_t ret = 0;
	// 사용자 정의 동작 

	device_create(&test_class, NULL, st_dev, NULL, "test_drv"); //노드 생성 

	//... 
}
```

### device table 
커널이 시작될 때 device driver코드에서 target으로 하는 하드웨어를 device tree에서 찾아 probe 함수를 동작 시키기 위해서는 device_id table을 명시해줘야 한다
```c
struct of_device_id {
    char *name;
    char *type;
    char *compatible;
    const void *data;
};
```

해당 구조체를 선언 후 식별에 필요한 필드에 데이터를 입력 하고난 후 
platform_driver structure에 of_device_id를 대입하면 된다. 
```c
static const struct of_device_id test_match[] = { 
						{ .compatible =
						"telechips,tdrv" },
						  {} };
						  
static struct platform_driver test_driver = 
{ .driver = { .name = "test_driver", 
			.of_match_table = test_match, // of_match_table 설정 
			.owner = THIS_MODULE, }, 
			.probe = test_probe, 
			.remove = test_remove, 
			};
```
#### 위와 같이 설정하게되면 platform device driver는 tset_driver라는 이름의 device tree를 찾고 있으면 probe 함수가 호출된다.

- of_device_id 를 설정할 때 { }  빈 중괄호를 표시해주는 이유는 배열을 끝을 표시하는 역할을 한다. 


## 함수 분석
 
[[device tree  접근]]


## 3. Makefile 작성

driver 파일을 빌드하여 .o 파일을 만들도록 Makefile을 작성해줘야한다

```Makefile
obj-$(CONFIG_TEST_DRV) += test_drv.o
```

$( ,,, ) 매크로를 정의 하기 위해서는 Kconfig를 작성해야한다

## 3. Kconfig 작성

플랫폼 드라이버는 커널 구성 시 활성화되는 드라이버이므로, Kconfig 파일에 해당 드라이버에 대한 정보를 포함하여 사용자 또는 빌드 시스템이 이를 설정할 수 있도록 해야한다
```
menuconfig test
	help
        This is test example driver

config TEST_DRV
	tristate "Test driver"
	default y
	help
        This is test example driver.


```

## 4. root Kconfig, Makefile에 추가된 내용 적용

기능 및 하드웨어 추가를 위한 작업을 한 것이기 떄문에 상위의 Kconfig, Makefile에 변경사항을 같이 빌드 및 적용할 수 있도록 내용을 추가 해줘야한다

- 상위 Kconfig
```
menu "Device Drivers"

//기존에 지원하던 device driver 

source "drivers/test/Kconfig" // 새로 추가한 프랫폼 디바이스 드라이버

endmenu
```

- 상위 Makefile
```Makefile
	//기존에 있던 드라이버의 Makefile 경로
obj-$(CONFIG_TEST_DRV)		+= test/
```




# Control Platform Driver

paltform driver가 정상적으로 등록이 되었다면 커널이 실행될때 해당 디바이스를 인식하여 deivce노드가 생성 되었을 것이다

![[Pasted image 20240730162838.png]]

device tree에서 test_drv라고 설정해놓은 디바이스를 paltform driver가 감지 하고 test_drv라는 이름의 노드를 생성 한 것 이다

- Control은 user space에서 진행하게 됨으로 drvier file을 열어서 값을 입력 해주거나 읽은 방식으로 진행된다

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/ioctl.h>
#include <fcntl.h>
#include <unistd.h>
#include <linux/ioctl.h>


    uint32_t data;

#define TEST_IOCTL_MAGIC     'n'
#define TEST_IOCTL_WRITE_REG _IOW(TEST_IOCTL_MAGIC,  4, struct reg_access_req *)
/**************************************************/

int main()
{
    int32_t fd = -1;
    int32_t ret = 0;
    int32_t flag = 1;
    char option;
    fd = open("/dev/test_drv", O_RDWR);  // device driver 노드 열기
    uint32_t data;

    if(fd < 0) 
    {
        printf("Cannot open device file...\n");
        return 0;
    }
    
    while(flag) 
    {
        printf("****Please Enter the Option*****\n");
        printf("        1. ioctl Write          \n");
        printf("        2. Exit                 \n");
        printf("********************************\n");
        printf("option : ");
        scanf(" %c", &option);
	    printf("Enter data : ");
		scanf("%d", &test_struct.data);
		ret = ioctl(fd, TEST_IOCTL_WRITE_REG, &data);
		printf("ioctl return : %d\n", ret);

    }

    close(fd);
	
	return 1;
}
```



[[device tree  접근]]
