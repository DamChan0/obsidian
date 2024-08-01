
> device driver 파일을 빌드 하고 보드에 옮긴 후 module 활성화 해보기


![[Pasted image 20240729130934.png]]

# Basic structuer

## source code
### 기본 헤더
```c
#include <linux/kernel.h>

#include <linux/module.h>

#include <linux/init.h>

#include <linux/fs.h>

#include <linux/device.h>
```
	
### device tree 지원을 위한 헤더
```c
#include <linux/of.h>

#include <linux/of_device.h>

#include <linux/of_gpio.h>

#include <linux/of_address.h>

#include <linux/of_irq.h>
```
#### `<linux/of.h>`

- **역할**: Device Tree의 기본적인 인터페이스를 정의합니다.
- **주요 기능**:
    - Device Tree 노드와 프로퍼티를 탐색하고 접근할 수 있는 다양한 함수와 매크로를 제공합니다.
    - `of_find_node_by_name()`, `of_get_property()`, `of_node_put()` 등의 함수가 포함되어 있습니다.

#### `<linux/of_device.h>`

- **역할**: Device Tree와 관련된 디바이스 인터페이스를 정의합니다.
- **주요 기능**:
    - Device Tree 노드로부터 디바이스 드라이버를 매칭하고 관리할 수 있는 함수들을 제공합니다.
    - `of_device_alloc()`, `of_device_register()`, `of_device_unregister()` 등의 함수가 포함되어 있습니다.

#### `<linux/of_gpio.h>`

- **역할**: Device Tree를 통해 GPIO 핀을 관리할 수 있는 인터페이스를 제공합니다.
- **주요 기능**:
    - Device Tree 노드로부터 GPIO 핀 번호와 설정을 가져오는 함수들을 제공합니다.
    - `of_get_named_gpio()`, `of_gpio_named_count()`, `of_gpio_get_active_pin()` 등의 함수가 포함되어 있습니다.

#### `<linux/of_address.h>`

- **역할**: Device Tree를 통해 메모리 주소를 관리할 수 있는 인터페이스를 제공합니다.
- **주요 기능**:
    - Device Tree 노드로부터 메모리 주소와 크기를 가져오는 함수들을 제공합니다.
    - `of_translate_address()`, `of_address_to_resource()`, `of_iomap()` 등의 함수가 포함되어 있습니다.

#### `<linux/of_irq.h>`

- **역할**: Device Tree를 통해 IRQ (Interrupt Request)를 관리할 수 있는 인터페이스를 제공합니다.
- **주요 기능**:
    - Device Tree 노드로부터 IRQ 번호와 설정을 가져오는 함수들을 제공합니다.
    - `of_irq_get()`, `of_irq_to_resource()`, `of_irq_count()` 등의 함수가 포함되어 있습니다.


#### 상세 내용 : [[device tree  접근]]


---


```c
#include <linux/module.h>   // Needed by all modules
#include <linux/kernel.h>   // kernel space api
#include <linux/init.h>     // kernel module initialization

// 모듈 라이선스 지정 (GPL, GPL v2, Dual BSD/GPL 등)
// 커널 2.6 부터 반드시 지정 필요
MODULE_LICENSE("GPL");
MODULE_AUTHOR("ZERODRAGON");
MODULE_DESCRIPTION("Moudle Programming - Hello Module");

static int __init module_begin (void) {
	printk(KERN_INFO "Hello, Linux Kernel.\n");
    return 0;
}

static void __exit module_end (void) {
    printk(KERN_INFO "Good Bye, Linux Kerenl!\n");
}

module_init(module_begin);
module_exit(module_end);
```

device driver는 기본적으로 init exit 함수를 가진다

### module_init
- module_init으로 등록한 함수가 insmod \<driver file name\> 수행시 호출되는 함수이다

### module_exit
- module_exit로 등록한 함수가 rmmod \<driver file name\>  수행시 호출되는 함수이다
## make file

- device driver 파일 형식인 .ko를 만들기 위해서는 modules 라는 옵션을 make 명령어와 같이 수행해야한다
```Makefile
# define module
obj-m += hello.o
obj-m += tcc_hello_driver.o
obj-m += tcc_hello_ioctl_driver.o
obj-m += auto_create_device.o


# set target kernel path
KDIR = ${Linux kernel build source}
// ex) KDIR = /home/B240801/work1/dongmin_ws/nn/build-autolinux/build/tcc7500-main/tmp/work/tcc7500_main-telechips-linux/linux-telechips/5.10.177-r0/build


# define test app info
TEST_APP := tcc_hello_app
TEST_APP_OBJ := tcc_hello_app.o

$(TEST_APP): $(TEST_APP_OBJ)
	$(CC) -o $@ $<

all: $(TEST_APP)
	make -C $(KDIR) M=$(PWD) modules

clean:
	make -C $(KDIR) M=$(PWD) clean
	rm -f $ $(TEST_APP) $(TEST_APP_OBJ)
```

> [!hint]+ 
> 
> 
해당 Makefile로 빌드를 진행하면 C파일의 오브젝트 파일(.o)파일과 디바이스 드라이버 모듈 파일( .ko ) 파일이 생성된다

---

# 하드웨어 제어를 위한 디바이스 드라이버

write 함수가 포함되어 있는 device driver를 다시 작성

```c
#include <linux/uaccess.h> // copy_to_user(), copy_from_user()
#include <linux/ioport.h>
#include <linux/module.h>
#include <linux/fs.h> // open(), close(), read(), write()
#include <linux/cdev.h>
#include <linux/io.h>   // ioremap(), iounmap()
#include <linux/gpio.h> // gpio~

int tcc_open(struct inode *inode, struct file *file){
//....
}

int tcc_release(struct inode *inode, struct file *file)
{
    pr_info("[%s] Kerner module is closed(%s)\n", __FUNCTION__, MOD_NAME);

    return 0;
}

ssize_t tcc_write(struct file *filp, const char *buf, size_t len, loff_t *off)
{
    uint32_t dst_reg;
    unsigned char in_char;

    // get input data
    get_user(in_char, buf);

    // select corresponding destination register
    dst_reg = (in_char == '1') ? 1 : 0;
    // pr_info()
    pr_info("input data : %c\n", in_char);
    pr_info("dst_reg : %d\n", dst_reg);
    // write register

    gpio_set_value(GPD_DISPLAY_IDX, dst_reg);

    pr_info("current pin value : %d\n", gpio_get_value(GPD_DISPLAY_IDX));
    return len;
}

static struct file_operations gpio_lcd_fops = {
    .write = tcc_write,
    .open = tcc_open,
    .release = tcc_release,
};

/*
_init_ 함수, _exit_ 함수 및 각 함수 등록하는 과정 
*/
```

위와 같이 open release 이외에 write함수를 추가하여 드라이버로 들어오는 입력을 복사 (`get_user()` ) 하여 컨드롤 하고자하는 하드웨어 (gpio)의 값에 써주는 함수 (    `gpio_set_value(GPD_DISPLAY_IDX, dst_reg);` )를 추가적으로 가지고 있어야 한다, 

##  get_user() 
	- `get_user` 함수는 리눅스 커널에서 사용자 공간에서 커널 공간으로 데이터를 안전하게 복사하기 위해 사용되는 함수. 이 함수는 `uaccess.h` 헤더 파일에 정의되어 있고 `get_user` 함수는 사용자 공간에서 커널 공간으로 단일 값을 복사하는 데
	  사용됨
## void gpio_set_value(unsigned gpio, int value);
	- `gpio_set_value`함수는 리눅스 커널에서 GPIO(General Purpose Input/Output) 핀의 값을 설정하는 데 사용되며. 이 함수는 `linux/gpio.h` 헤더 파일에 정의되어 있습니다.
## int gpio_get_value(unsigned gpio);
	- `gpio_get_value()` 함수는 특정 gpio의 value값을 가져오는 함수로 현재 핀에 입력된 값이 얼마인지 확인 할 수 있다


> [!tip]+ print 함수 종류
> - printk : 커널에 로그를 찍는데 로그의 레벨을 설정해줘야한다
> 
> - pr_xxxx
>    - pr_info, pr_err, pr_warn이 있으며 printk에서 레벨을 매번 지정하지 않고 레벨을 함수에서 자동으로 지정해주는 방식의 함수이다
