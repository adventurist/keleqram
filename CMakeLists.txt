
cmake_minimum_required(VERSION 2.8.4)
project(kiq_telegram)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17 -Wall -g")
set(Boost_USE_MULTITHREADED ON)
set (CPR_BUILD_TESTS OFF)
include(FetchContent)
FetchContent_Declare(cpr GIT_REPOSITORY https://github.com/libcpr/cpr.git GIT_TAG f4622efcb59d84071ae11404ae61bd821c1c344b) # the commit hash for 1.6.2
FetchContent_MakeAvailable(cpr)

find_package(Threads REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(Boost COMPONENTS system REQUIRED)
find_package(CURL)
include_directories(/usr/local/include src ${OPENSSL_INCLUDE_DIR} ${Boost_INCLUDE_DIR} third_party/inih/cpp)
if (CURL_FOUND)
    include_directories(${CURL_INCLUDE_DIRS})
    add_definitions(-DHAVE_CURL)
endif()
set (BUILD_SHARED_LIBS ON)
add_subdirectory(${nlohmann_json_source} third_party/json)
add_subdirectory(third_party/tgbot-cpp)

set(SOURCE_FILES
  src/keleqram_bot.cpp
  src/util.cpp
  third_party/inih/ini.c
  third_party/inih/cpp/INIReader.cpp
)

add_library(keleqram STATIC ${SOURCE_FILES})
add_executable(${PROJECT_NAME} src/main.cpp)

target_link_libraries(keleqram TgBot ${CMAKE_THREAD_LIBS_INIT} ${OPENSSL_LIBRARIES} ${Boost_LIBRARIES} ${CURL_LIBRARIES} cpr::cpr nlohmann_json::nlohmann_json)
target_link_libraries(${PROJECT_NAME} keleqram)
