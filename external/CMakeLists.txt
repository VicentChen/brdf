cmake_minimum_required(VERSION 3.12)

project(brdfDependencies)

set(CMAKE_CXX_STANDARD 20)

# Following lines are configurations need to modify.
set(COMPILE_THREAD 16)
set(ENV{http_proxy}  "http://127.0.0.1:10809") # for http
set(ENV{https_proxy} "http://127.0.0.1:10809") # for https

if(NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE RelWithDebInfo CACHE STRING
		"Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
		FORCE)
endif(NOT CMAKE_BUILD_TYPE)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

set(DEPENDENCIES_DOWNLOAD_DIR "${CMAKE_CURRENT_SOURCE_DIR}/Downloads")
set(DEPENDENCIES_BINARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}")

set(BUILD_COMMAND_OPTS --target install --config ${CMAKE_BUILD_TYPE})
file(MAKE_DIRECTORY ${DEPENDENCIES_DOWNLOAD_DIR})
file(MAKE_DIRECTORY ${DEPENDENCIES_BINARY_DIR}/include)
file(MAKE_DIRECTORY ${DEPENDENCIES_BINARY_DIR}/lib)
file(MAKE_DIRECTORY ${DEPENDENCIES_BINARY_DIR}/bin)

function(DownloadExternal name url target md5)
	set(file_download_path "${DEPENDENCIES_DOWNLOAD_DIR}/${target}")
	if(NOT EXISTS ${file_download_path})
		message(STATUS "Downloading ${name}...")
		file(DOWNLOAD ${url} ${file_download_path} SHOW_PROGRESS EXPECTED_MD5 ${md5})
	endif()
	message(STATUS "${name} downloaded.")
endfunction(DownloadExternal)

function(CloneExternal name url)
	if(NOT EXISTS "${DEPENDENCIES_DOWNLOAD_DIR}/${name}")
		message(STATUS "Cloning ${name}...")
		execute_process(COMMAND "git" "clone" "--recurse-submodules" ${url} WORKING_DIRECTORY ${DEPENDENCIES_DOWNLOAD_DIR})
	endif()
	message(STATUS "${name} cloned")
endfunction()

# ########
# # zlib #
# ########
set(ZLIB_PROJECT_DIR "${DEPENDENCIES_DOWNLOAD_DIR}/zlib")
# file(MAKE_DIRECTORY ${ZLIB_PROJECT_DIR})
CloneExternal("zlib" "https://github.com/madler/zlib.git")
execute_process(COMMAND ${CMAKE_COMMAND} 
	-DCMAKE_INSTALL_PREFIX=${DEPENDENCIES_BINARY_DIR}
	-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
	-G ${CMAKE_GENERATOR}
	-j${COMPILE_THREAD}
	-DCMAKE_GENERATOR_PLATFORM=${CMAKE_GENERATOR_PLATFORM}
	-DCMAKE_MAKE_PROGRAM=${CMAKE_MAKE_PROGRAM}
	${ZLIB_PROJECT_DIR}
	WORKING_DIRECTORY ${ZLIB_PROJECT_DIR}
)
execute_process(COMMAND ${CMAKE_COMMAND} --build ${ZLIB_PROJECT_DIR} ${BUILD_COMMAND_OPTS})

########
# glew #
########
set(GLEW_PROJECT_DIR "${DEPENDENCIES_DOWNLOAD_DIR}/glew-2.2.0")
file(MAKE_DIRECTORY ${GLEW_PROJECT_DIR})
DownloadExternal("glew-2.2.0" "https://github.com/nigels-com/glew/releases/download/glew-2.2.0/glew-2.2.0-win32.zip" "glew-2.2.0-win32.zip" "1FEDDFE8696C192FA46A0DF8EAC7D4BF")
execute_process(COMMAND ${CMAKE_COMMAND} -E tar xf glew-2.2.0-win32.zip WORKING_DIRECTORY ${DEPENDENCIES_DOWNLOAD_DIR})
file(GLOB GLEW_INC "${GLEW_PROJECT_DIR}/include/*")
file(GLOB GLEW_LIB "${GLEW_PROJECT_DIR}/lib/Release/x64/*")
file(GLOB GLEW_BIN "${GLEW_PROJECT_DIR}/bin/Release/x64/*")
file(COPY ${GLEW_INC} DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/include)
file(COPY ${GLEW_LIB} DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/lib)
file(COPY ${GLEW_BIN} DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/bin)

########
# glut #
########
set(GLUT_PROJECT_DIR "${DEPENDENCIES_DOWNLOAD_DIR}/freeglut-3.2.1")
file(MAKE_DIRECTORY ${GLUT_PROJECT_DIR})
DownloadExternal("glut-3.2.1" "http://prdownloads.sourceforge.net/freeglut/freeglut-3.2.1.tar.gz?download" "freeglut-3.2.1.tar.gz" "CD5C670C1086358598A6D4A9D166949D")
execute_process(COMMAND ${CMAKE_COMMAND} -E tar xf freeglut-3.2.1.tar.gz WORKING_DIRECTORY ${DEPENDENCIES_DOWNLOAD_DIR})
execute_process(COMMAND ${CMAKE_COMMAND} 
	-DCMAKE_INSTALL_PREFIX=${DEPENDENCIES_BINARY_DIR}
	-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
	-G ${CMAKE_GENERATOR}
	-j${COMPILE_THREAD}
	-DCMAKE_GENERATOR_PLATFORM=${CMAKE_GENERATOR_PLATFORM}
	-DCMAKE_MAKE_PROGRAM=${CMAKE_MAKE_PROGRAM}
	${GLUT_PROJECT_DIR}
	WORKING_DIRECTORY ${GLUT_PROJECT_DIR}
)
execute_process(COMMAND ${CMAKE_COMMAND} --build ${GLUT_PROJECT_DIR} ${BUILD_COMMAND_OPTS})