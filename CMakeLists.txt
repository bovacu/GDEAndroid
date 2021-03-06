cmake_minimum_required(VERSION 3.18.1)

# Instruct our compiler that we want to use C++ 17 and use exceptions.
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17 -fexceptions")

# Determine what build type we are in, shifted to lower case (debug or release).
string(TOLOWER ${CMAKE_BUILD_TYPE} BUILD_VARIANT_DIR)
set(GDE_PATH "/home/borja/Documents/GDE")

# Location of our third party libraries.
set(LIBS "${GDE_PATH}/submodules")
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH})

set(SDL_DIR "sdl/jni/SDL")
set(SDL_MIXER_DIR "sdl/jni//SDL_mixer")
set(SDL_NET_DIR "${LIBS}/SDL_net")
set(SDL_IMAGE_DIR "sdl/jni/SDL_image")
set(FREETYPE_DIR "${LIBS}/freetype")
set(GLM_DIR "${LIBS}/glm")
set(YAML_CPP_DIR "${GDE_PATH}/submodules/yaml-cpp")
set(ENTT_DIR "${GDE_PATH}/submodules/entt")
set(BOX2D_DIR "${GDE_PATH}/submodules/box2d")

set(MAIN_SOURCE_DIR "${GDE_PATH}/engine")
set(SANDBOX_SOURCE_DIR "${GDE_PATH}/sandbox")

set(EXTERNAL_STB "${GDE_PATH}/engine/include/stb")
set(EXTERNAL_IMGUI "${GDE_PATH}/submodules/imgui")

# Location of the .so files generated by the 'sdl' module.
set(SDL_SO_DIR "${CMAKE_SOURCE_DIR}/sdl/build/intermediates/ndkBuild/${BUILD_VARIANT_DIR}/lib/${ANDROID_ABI}")


# This is the main SDL library target, we will name it 'sdl2'.
add_library(
        sdl2
        SHARED
        IMPORTED
)

add_library(
        sdl2_mixer
        SHARED
        IMPORTED
)

add_library(
        sdl2_image
        SHARED
        IMPORTED
)

add_library(
        mpg123
        SHARED
        IMPORTED
)

add_library(
        box2d
        SHARED
        IMPORTED
)

add_library(
        yaml-cpp
        SHARED
        IMPORTED
)


# This is the built in NDK logging library
find_library(log-lib log)
add_subdirectory(freetype/freetype)
add_subdirectory(glm/glm)
add_subdirectory(entt/entt)
#add_subdirectory(box2d/box2d)
#add_subdirectory(yaml-cpp/yaml-cpp)

set_target_properties(
        sdl2
        PROPERTIES
        IMPORTED_LOCATION
        ${SDL_SO_DIR}/libSDL2.so
)

set_target_properties(
        sdl2_mixer
        PROPERTIES
        IMPORTED_LOCATION
        ${SDL_SO_DIR}/libSDL2_mixer.so
)

set_target_properties(
        sdl2_image
        PROPERTIES
        IMPORTED_LOCATION
        ${SDL_SO_DIR}/libSDL2_image.so
)

set_target_properties(
        mpg123
        PROPERTIES
        IMPORTED_LOCATION
        ${SDL_SO_DIR}/libmpg123.so
)

set_target_properties(
        box2d
        PROPERTIES
        IMPORTED_LOCATION
        ${SDL_SO_DIR}/libbox2d.so
)

set_target_properties(
        yaml-cpp
        PROPERTIES
        IMPORTED_LOCATION
        ${SDL_SO_DIR}/libyaml-cpp.so
)


# We will tell our build system where to look for third party header files.
include_directories(${SDL_DIR}/include)
include_directories(${SDL_MIXER_DIR}/include)
include_directories(${SDL_NET_DIR}/include)
include_directories(${SDL_IMAGE_DIR})
include_directories(${FREETYPE_DIR}/include)
include_directories(${GLM_DIR})
include_directories(${MAIN_SOURCE_DIR}/include)
include_directories(${SANDBOX_SOURCE_DIR})
include_directories(${EXTERNAL_STB})
include_directories(${YAML_CPP_DIR}/include/)
include_directories(${ENTT_DIR}/src/)
include_directories(${BOX2D_DIR}/include/)
include_directories(.)
include_directories(../../code/include)

# We will recursively collate all .hpp header and .cpp source files under the main source directory as the input for the compilation.
file(GLOB_RECURSE CPP_HEADERS ${MAIN_SOURCE_DIR}/include/*.h*  ../../code/include/*.h*)
file(GLOB_RECURSE CPP_SOURCES ${MAIN_SOURCE_DIR}/src/*.cpp   ../../code/src/*.cpp)

set(GL_PROFILES "GL_PROFILE_GLES2" "GL_PROFILE_GLES3")

set(TARGET_LIBRARIES
        sdl2
        sdl2_mixer
#        sdl2_net
        sdl2_image
        mpg123
        freetype
        glm
        yaml-cpp
        box2d
        ${log-lib}
        GLESv3
        EGL
    )

#GDEFirebase

add_library(
        GDEAndroid
        SHARED
        app/main.cpp
        ${CPP_HEADERS}
        ${CPP_SOURCES}
)

# Finally, we need to link all the libraries we want to use in our project together, remembering that the 'a-simple-triangle' library is in fact our own C++ code. Note also we are including the 'GLESv2' library for OpenGL ES2 support, though we don't need to define it ourselves with the 'add_library' commands.
target_link_libraries(
        GDEAndroid
        ${TARGET_LIBRARIES}
)

target_compile_definitions(GDEAndroid PRIVATE ${GL_PROFILES})
