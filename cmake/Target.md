
> [!example]+ Target
> install( Target ... ) 
> 등등 cmake에서 사용되는 target의 의미 설명


# Target 이란

- add_executable 또는 add_library를 이용해서 만들어진 바이너리 또는 라이브러리를 말한다.  따라서 Target을 이용하는 명령어를 수행하기 위해서는 위의 두 명령어가 선행되어야한다

### 예제

```cmake
# CMake 최소 버전 설정
cmake_minimum_required(VERSION 3.10)

# 프로젝트 이름 및 버전 설정
project(MyProject VERSION 1.0)

# 실행 파일 타겟 생성
add_executable(MyExecutable main.cpp)

# 라이브러리 타겟 생성
add_library(MyLibrary STATIC mylib.cpp)

# 타겟 간의 의존성 설정
target_link_libraries(MyExecutable PRIVATE MyLibrary)

# 컴파일 옵션 설정
target_compile_options(MyExecutable PRIVATE -Wall -Wextra)

```