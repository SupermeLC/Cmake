# Cmake学习笔记

> 作者： SupermeLiu

> 参考： https://github.com/ttroy50/cmake-examples

---
## 常用变量
> 查阅：https://www.cnblogs.com/xianghang123/p/3556425.html

---
## 命令
指定cmake版本
```cmake
cmake_minimum_required(VERSION 3.5)
```
项目命名
```cmake
project(project)
```

添加子目录
```cmake
add_subdirectory(filefolder)
```

将若干文件整合为 SOURCES
```cmake
set(SOURCES src/Hello.cpp src/main.cpp)
```

生成可执行文件（二进制）
```cmake
add_executable(${PROJECT_NAME} main.cpp)
```

添加库
 ```cmake
add_library(${PROJECT_NAME} lib.cpp)
# 无cpp文件时，可使用 INTERFACE
add_library(${PROJECT_NAME} INTERFACE)
# 将库文件重新命名，可使用 ALIAS  
add_library(sub::lib1 ALIAS ${PROJECT_NAME})
 ```

指定include文件位置
```cmake
target_include_directories(${PROJECT_NAME} 
	PUBLIC 
		${PROJECT_SOURCE_DIR}/include
)
```

指定需要的外部库
```cmake
target_link_libraries(${PROJECT_NAME} sub::lib1 sub::lib2)
```

将ver.h中信息注入ver.h.in
 ```cmake
# 可用于生成路径，版本号等
configure_file(ver.h.in ver.h)
 ```
> 代码版本命名：https://blog.csdn.net/u012107143/article/details/78731272

install函数
 ```cmake
# 二进制文件
install(TARGETS cmake_examples_inst_bin
        DESTINATION bin)

# 库文件
install(TARGETS cmake_examples_inst
        LIBRARY DESTINATION lib)

# 头文件
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ 
        DESTINATION include)

# 配置信息
install(FILES cmake-examples.conf
        DESTINATION etc)
 ```
生成deb安装包
 ```cmake
# Tell CPack to generate a .deb package
set(CPACK_GENERATOR "DEB")

# Set a Package Maintainer.
# This is required
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Thom Troy")

# Set a Package Version
set(CPACK_PACKAGE_VERSION ${deb_example_VERSION})

# Include CPack
include(CPack)
 ```

```bash
$ mkdir build
$ cd build
$ cmake ..
$ make package
```

---
## 运行方法
```bash
$ mkdir build
$ cd build
$ cmake ..
$ make
```

打印信息更加全面
```bash
$ make VERBOSE=1
```

---
## 其他功能
### 检查代码错误

安装cppcheck

```bash
$ sudo apt-get install cppcheck
```

项目tree
```bash
$ tree
.
├── cmake
│	└── modules
│       	└── FindCppCheck.cmake
├── CMakeLists.txt
└── subproject1
	├── CMakeLists.txt
	└── main1.cpp
```

* github中提供了一个FindCppCheck.cmake的文件（不知道什么原理）
* main1.cpp中存在错误：
  ```c++
  #include <iostream>
  
  int main(int argc, char *argv[]){
      std::cout << "Hello Main!" << std::endl;
      char tmp[10];
      tmp[11] = 's';
      return 0;
  }
  ```

* CMakeFiles.txt
  ```cmake
  cmake_minimum_required (VERSION 3.5)
  
  project(cppcheck_analysis)
  
  # Have cmake create a compile database
  set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
  
  # Add a custom CMake Modules directory
  set(CMAKE_MODULE_PATH 
  		${CMAKE_SOURCE_DIR}/cmake/modules
          ${CMAKE_MODULE_PATH}
  )
  
  # find the cppcheck binary
  find_package(CppCheck)
  
  # Add sub directories
  add_subdirectory(subproject1)
  
  add_executable(${PROJECT_NAME} subproject1/main1.cpp)
  ```

运行方式
```bash
$ mkdir build
$ cd build
$ cmake ..
$ make cppcheck-analysis
```

### 单元测试

暂时没搞明白有什么用处