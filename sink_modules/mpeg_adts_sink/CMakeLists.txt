cmake_minimum_required(VERSION 3.13)
project(mpeg_adts_sink)

file(GLOB SRC "src/*.cpp")

set(DEPNAME libmp3lame-CMAKE)
if (EXISTS ${CMAKE_BINARY_DIR}/${DEPNAME}/${DEPNAME}.tar.gz)
    message("Not downloading ${DEPNAME}.")
else ()
    message("Downloading ${DEPNAME}")
    file(DOWNLOAD https://codeload.github.com/Iunusov/libmp3lame-CMAKE/tar.gz/master ${CMAKE_BINARY_DIR}/${DEPNAME}/${DEPNAME}.tar.gz)
    message("Extracting ${DEPNAME}.")
    execute_process(COMMAND ${CMAKE_COMMAND} -E tar xfz ${CMAKE_BINARY_DIR}/${DEPNAME}/${DEPNAME}.tar.gz WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/${DEPNAME}/)
    file(RENAME ${CMAKE_BINARY_DIR}/${DEPNAME}/${DEPNAME}-master ${CMAKE_BINARY_DIR}/${DEPNAME}/${DEPNAME}-root)
    file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/${DEPNAME}/build)
    execute_process(COMMAND ${CMAKE_COMMAND} ../${DEPNAME}-root -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/${DEPNAME}/build)
    execute_process(COMMAND ${CMAKE_COMMAND} --build  .  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/${DEPNAME}/build)
endif ()

include(${SDRPP_MODULE_CMAKE})

target_link_directories(${PROJECT_NAME} PUBLIC ${CMAKE_BINARY_DIR}/${DEPNAME}/build)
target_link_libraries(${PROJECT_NAME} PUBLIC mp3lame)
target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_BINARY_DIR}/${DEPNAME}/${DEPNAME}-root/include)
