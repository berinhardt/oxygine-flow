cmake_minimum_required (VERSION 3.10)
project (OXYGINE_FLOW)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

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
