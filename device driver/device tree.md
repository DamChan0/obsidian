
```bash
/dts-v1/;
/ {
    model = "Example Device";
    compatible = "example,device";

    cpus {
        cpu@0 {
            compatible = "arm,cortex-a7";
            reg = <0>;
        };
    };

    memory {
        device_type = "memory";
        reg = <0x80000000 0x20000000>;
    };

    soc {
        compatible = "simple-bus";
        #address-cells = <1>;
        #size-cells = <1>;

        uart@101f1000 {
            compatible = "ns16550a";
            reg = <0x101f1000 0x1000>;
            interrupt-parent = <&intc>;
            interrupts = <1 3>;
        };
    };

    intc: interrupt-controller {
        compatible = "arm,gic";
        interrupt-controller;
        #interrupt-cells = <3>;
    };
};

```

---

## 루트 노드

- / 로 루트를 표현하고 그 밑에 각 노드 들에 대한 정보를 기입한다

## 노드

- 각 하드웨어의 속성을 나타내며 부모-자식 관계를 가지고 있다
    
- **이름@장치주소** 의 형태로 구성된다.
    
- 예시
    
    ```bash
    	/ {
            node1 {
                a-string-property = "A string";
                a-string-list-property = "first string", "second string";
                a-byte-data-property = [0x01 0x23 0x34 0x56];
                child-node1 {
                    first-child-property;
                    second-child-property = <1>;
                    a-string-property = "Hello, world";
                };
                child-node2 {
                }
            };
            node2 {
                an-empty-property;
                a-cell-property = <1 2 3 4>; /* each number (cell) is a uint32 */
                child-node1 {
                };
            };
        };
    ```
    

---

## 프로퍼티

- 노드의 속성을 말하며 key- value 쌍으로 되어 있다.

### 디바이스 트리 프로퍼티 값 설정 예제

1. **문자열(String)**
    
    - NULL로 끝나는 문자열은 `""`를 사용합니다.
    - 예제: `string-property = "a string";`
2. **32비트 부호없는 정수형(Unsigned 32-bit integer)**
    
    - 32비트 정수는 `<>`를 사용합니다.
    - 예제: `cell-property = <0xbeef 123 0xabcd1234>;`
3. **2진 데이터(Binary data)**
    
    - 2진 데이터는 `[]`를 사용합니다.
    - 예제: `binary-property = [0x01 0x23 0x45 0x67];`
4. **혼합 데이터(Mixed data)**
    
    - 혼합 데이터는 `,`로 구분합니다.
    - 예제: `mixed-property = "a string", [0x01 0x23 0x45 0x67], <0x12345678>;`
5. **문자열 나열(String list)**
    
    - 문자열 나열은 `""`로 묶어 `,`로 구분합니다.
    - 예제: `string-list = "red fish", "blue fish";`
    
    ```bash
    /dts-v1/;
    / {
        model = "Example Device";
        compatible = "example,device";
    
        example-node {
            string-property = "a string";
            cell-property = <0xbeef 123 0xabcd1234>;
            binary-property = [0x01 0x23 0x45 0x67];
            mixed-property = "a string", [0x01 0x23 0x45 0x67], <0x12345678>;
            string-list = "red fish", "blue fish";
        };
    };
    
    ```
    
    ### compatible Property
    
    - 각 노드가 어떤 디바이스에 포함되는지에 대한 설명
        
    - 드라이버가 특정 하드웨어를 인식 할 수 있도록 한다
        
        ```bash
        uart0: serial@7e201000 {
            compatible = "brcm,bcm2835-uart", "simple-uart";
            reg = <0x7e201000 0x1000>;
            interrupts = <1 25>;
        };
        
        ```
        
        - **"brcm,bcm2835-uart"**:
            - 이 문자열은 Broadcom의 BCM2835 SoC에 있는 UART 장치와 호환됨을 나타냅니다. 커널은 이 문자열을 기반으로 특정 드라이버를 찾아 장치를 초기화합니다.
        - **"simple-uart"**:
            - 이 문자열은 일반적인 UART 드라이버와 호환됨을 나타냅니다. 만약 커널이 첫 번째 문자열에 맞는 드라이버를 찾지 못하면, 두 번째 문자열에 맞는 드라이버를 찾게 됩니다.

### reg

- 하드웨어의 주소와 크기를 결정해준다
    
- ## reg = <0x0C00 0x0 0xFFFF02 0x3333>;
    `reg = <base_address size>;`
- 이때 각 hex 값들을 cell 이라고 한다
    ```bash
    /soc {
        #address-cells = <1>;
        #size-cells = <1>;
    
        uart0: serial@0C00 {
            reg = <0x0C00 0x1000>;  // 주소 0x0C00, 크기 0x1000 (각각 하나의 셀)
        };
    };
    
    ```
reg 값은 여러 쌍이 올 수 있다. 

[[device tree  접근#reg 접근]]




### cpu
    
    - cpu는 id로 주소를 부여한다
    
    ```bash
    /cpus {
        #address-cells = <1>;
        #size-cells = <0>;
    
        cpu@0 {
            device_type = "cpu";
            reg = <0>;
        };
    
        cpu@1 {
            device_type = "cpu";
            reg = <1>;
        };
    };
    ```
    

---

# 파일 분리

- 파일 트리
    
    - pinctrl 같은 경우 main dts 와 분리 후 include 하여 빌드하는 방식으로 사용할 수 있다
    
    ### Pinctrl dts
    
    ```bash
    /dts-v1/;
    
    / {
        pinctrl: pinctrl {
            pinctrl_gpios: pinctrl_gpios {
                pinmux = <0x10 0x3 0x1>; // GPIO 핀 설정 예시
            };
        };
    };
    
    ```
    
    ### Main dts
    
    ```bash
    /dts-v1/;
    /include/ "pinctrl.dtsi"
    
    / {
        model = "Complex Device Example with Separate Pinctrl";
        compatible = "complex,device";
    
        cpus {
            cpu@0 {
                compatible = "arm,cortex-a7";
                reg = <0>;
            };
        };
    
        memory {
            device_type = "memory";
            reg = <0x80000000 0x40000000>;  // 1GB 메모리
        };
    
        soc {
            compatible = "simple-bus";
            #address-cells = <1>;
            #size-cells = <1>;
            ranges;
    
            uart@101f1000 {
                compatible = "ns16550a";
                reg = <0x101f1000 0x1000>;
                interrupt-parent = <&intc>;
                interrupts = <1 3>;
            };
    
            i2c@101f2000 {
                compatible = "i2c-designware";
                reg = <0x101f2000 0x1000>;
                interrupt-parent = <&intc>;
                interrupts = <2 4>;
                clock-frequency = <400000>;
    
                rtc@68 {
                    compatible = "dallas,ds3231";
                    reg = <0x68>;
                };
            };
    
            gpio@101f3000 {
                compatible = "gpio-dwapb";
                reg = <0x101f3000 0x1000>;
                interrupt-parent = <&intc>;
                interrupts = <3 5>;
    
                gpio-controller;
                #gpio-cells = <2>;
                pinctrl-0 = <&pinctrl_gpios>;
            };
        };
    
        intc: interrupt-controller {
            compatible = "arm,gic";
            interrupt-controller;
            #interrupt-cells = <3>;
        };
    };
    
    ```