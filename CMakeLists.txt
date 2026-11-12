cmake_minimum_required (VERSION 3.16)

# Android keeps clang's default C++ dialect: the ndk-build baseline compiled
# this module at that default, and a byte comparison of the two builds needs
# the same codegen. The Emscripten and native graphs keep the C++11 pin.
if (NOT ANDROID)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
endif()

# Android.mk hands the compiler its sources in the order LOCAL_SRC_FILES lists
# them, the archiver keeps that member order, and the final link layout follows
# it, so the .cpp entries below follow Android.mk's sequence (flow.cpp,
# Transition.cpp, Scene.cpp) rather than a folder-by-folder one.
add_library(oxygine-flow STATIC 
	src/flow/flow.cpp 
	src/flow/Scene.h
	src/flow/Transition.cpp 
	src/flow/flow.h
	src/flow/Scene.cpp 
	src/flow/Transition.h
)
target_link_libraries(oxygine-flow PUBLIC oxygine-framework)
target_include_directories(oxygine-flow PUBLIC src)
if (ANDROID)
   # Android.mk reached the framework headers through
   # '$(LOCAL_PATH)/../oxygine-framework/oxygine/src/', listed in
   # LOCAL_C_INCLUDES. ndk-build therefore recorded that spelling in __FILE__
   # for the framework headers this module includes, relative to the module's
   # own directory (e.g.
   # '../../deps/oxygine-flow/../oxygine-framework/oxygine/src/oxygine/...').
   # The inherited include directory is the collapsed absolute one, which the
   # root prefix map rewrites to '../../deps/oxygine-framework/oxygine/src/...'.
   # The literal lives in this module's own translation units, so a PRIVATE
   # directory is enough; BEFORE puts it ahead of the inherited collapsed
   # directory, so clang opens the header through the spelling ndk-build used.
   # The include directory alone is sufficient: no matching prefix map is
   # needed.
   target_include_directories(oxygine-flow BEFORE PRIVATE
      "${CMAKE_CURRENT_SOURCE_DIR}/../oxygine-framework/oxygine/src")
   # The rest of LOCAL_C_INCLUDES: the glm and SDL spellings ndk-build used.
   target_include_directories(oxygine-flow BEFORE PRIVATE
      "${CMAKE_CURRENT_SOURCE_DIR}/../glm/"
      "${CMAKE_CURRENT_SOURCE_DIR}/../SDL/include/")
endif()
