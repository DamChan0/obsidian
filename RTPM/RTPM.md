# 구조 요약

^0209d7

![[Drawing 2024-07-24 14.57.37.excalidraw|1500]]

## tc_nn_app

- tc_nn_app은 총 3가지의 input을 받을 수 있다
    - **rtpm** =로컬에 있는 파일
    - **Sensor** = 센서로부터 실시간 data
    - **Files** = 로컬에 있는 파일
      
- rtpm 과 file 입력의 차이점
    - rtpm은 폴더와 image, video를 입력 할 수 있다.
    - files은 image 만 입력 가능
    

### 기능
1. 카메라 해상도 W/H 조절
2. 출력 해상도 W/H 조절
3. InPut / Output
	1. camera
	2. rtpm
	3. file
	4. [[Vision Protocol]]
4. path 지정
	5. -p {your_path}
5. 신경망 지정가능
6. 모델 지정가능


