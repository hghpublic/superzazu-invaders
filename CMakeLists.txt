cmake_minimum_required(VERSION 4.2)

project(invaders
  LANGUAGES C
  DESCRIPTION "A Space Invaders emulator"
  HOMEPAGE_URL "https://github.com/superzazu/invaders"
)

# fetch dependencies


include(FetchContent)

# i8080

FetchContent_Declare(
  i8080
  GIT_REPOSITORY https://github.com/hghpublic/superzazu-8080.git
  GIT_TAG        dev/0
)

# SDL_nmix

# FetchContent_Declare(
#   i8080
#   GIT_REPOSITORY https://github.com/superzazu/SDL_nmix.git
#   GIT_TAG        dev/0
# )

# SDL_sound

# FetchContent_Declare(
#   i8080
#   GIT_REPOSITORY https://github.com/superzazu/SDL_nmix.git
#   GIT_TAG        dev/0
# )


FetchContent_MakeAvailable(i8080)

# invaders

set(SOURCES
  src/main.c
  src/invaders.c
)
set(ROMS_DIR "./roms/" CACHE STRING "Path to directory containing rom files")

set(target invaders)
add_executable(${target} ${SOURCES})
target_compile_features(${target} PRIVATE c_std_23)
if (MSVC)
  target_compile_options(${target} PRIVATE /W4)
else()
  target_compile_options(${target} PRIVATE -Wall -Wextra -pedantic -Wno-gnu-binary-literal)
endif()

if (EMSCRIPTEN)
  set(CMAKE_EXECUTABLE_SUFFIX ".html")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -s USE_SDL=2")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} --preload-file ${ROMS_DIR}@/roms")
  # stub file to avoid errors on "find_package(SDL2 REQUIRED)" in SDL_sound
  set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/src/cmake")
else()
  find_package(SDL2 REQUIRED)
  target_include_directories(${target} PRIVATE ${SDL2_INCLUDE_DIRS})
  target_link_libraries(${target} PRIVATE ${SDL2_LIBRARIES})
endif()

# SDL2_sound
option(SDLSOUND_DECODER_WAV "" ON)
option(SDLSOUND_DECODER_AIFF "" OFF)
option(SDLSOUND_DECODER_AU "" OFF)
option(SDLSOUND_DECODER_VOC "" OFF)
option(SDLSOUND_DECODER_FLAC "" OFF)
option(SDLSOUND_DECODER_VORBIS "" OFF)
option(SDLSOUND_DECODER_RAW "" OFF)
option(SDLSOUND_DECODER_SHN "" OFF)
option(SDLSOUND_DECODER_MODPLUG "" OFF)
option(SDLSOUND_DECODER_MP3 "" OFF)
option(SDLSOUND_DECODER_COREAUDIO "" OFF)
option(SDLSOUND_BUILD_STATIC "" ON)
option(SDLSOUND_BUILD_SHARED "" OFF)
option(SDLSOUND_BUILD_TEST "" OFF)

# target_link_libraries(${target} PRIVATE SDL2_sound-static)
target_link_libraries(${target} PRIVATE i8080)
target_include_directories(${target} PRIVATE src)
