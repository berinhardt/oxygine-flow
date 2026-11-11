cmake_minimum_required (VERSION 3.16)

# Android keeps clang's default C++ dialect: the ndk-build baseline compiled
# this module at that default, and a byte comparison of the two builds needs
# the same codegen. The Emscripten and native graphs keep the C++11 pin.
if (NOT ANDROID)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
endif()

add_library(oxygine-flow STATIC 
	src/flow/Scene.cpp 
	src/flow/Scene.h
	src/flow/flow.cpp 
	src/flow/flow.h
	src/flow/Transition.cpp 
	src/flow/Transition.h
)
target_link_libraries(oxygine-flow PUBLIC oxygine-framework)
target_include_directories(oxygine-flow PUBLIC src)
