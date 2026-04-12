cmake_minimum_required(VERSION 3.10)
project(MPP_cmake)

set(CMAKE_CXX_STANDARD 14)
option(BUILD_DEMO "IF BUILD LIB WITH DEMO" ON)

option(MPP_BUILD_FACE_DETECTOR "Build face detector" OFF)
option(MPP_BUILD_FACE_LANDMARKER "Build face landmarker" OFF)
option(MPP_BUILD_HAND_DETECTOR "Build hand detector" ON)
option(MPP_BUILD_HAND_LANDMARKER "Build hand landmarker" ON)
option(MPP_BUILD_POSE_DETECTOR "Build pose detector" ON)
option(MPP_BUILD_POSE_LANDMARKER "Build pose landmarker" ON)
option(MPP_BUILD_INTERACTIVE_SEGMENTER "Build interactive segmenter" OFF)
option(MPP_BUILD_HAIR_SEGMENTER "Build hair segmenter" OFF)
option(MPP_BUILD_SELFIE_SEGMENTER "Build selfie segmenter" OFF)
option(MPP_BUILD_IRIS_TRACKER "Build iris tracker" OFF)
option(MPP_BUILD_HOLISTIC_TRACKER "Build holistic tracker" OFF)

if (BUILD_DEMO)
    message(STATUS "BUILD_DEMO is ON")
else ()
    message(STATUS "BUILD_DEMO is OFF")
endif ()

set(OpenCV_DIR "" CACHE PATH "Path to OpenCV/opencv_lite build directory (REQUIRED)")

# Make OpenCV_DIR mandatory
if (NOT DEFINED OpenCV_DIR OR OpenCV_DIR STREQUAL "")
    message(FATAL_ERROR "OpenCV_DIR is required but not set. Please specify it with -DOpenCV_DIR=/path/to/opencv/build")
endif ()

# for android platform, we need assign the platform based lib, at leaset the "arm64-v8a" and "armeabi-v7a"
# Please orgnize the folder like the following:
# lib:
#   - arm64-v8a, it should contains: libMNN.so libopencv_core.so libopencv_dnn.so libopencv_imgproc.so
#   - armeabi-v7a, it should contains: libMNN.so libopencv_core.so libopencv_dnn.so libopencv_imgproc.so
#   - include/opencv2
if (ANDROID)
    include_directories("${OpenCV_DIR}/include")
    set(OpenCV_DIR_Android "")
    if (ANDROID_ABI STREQUAL "arm64-v8a")
        set(OpenCV_DIR_Android "${OpenCV_DIR}/arm64-v8a")
    elseif (ANDROID_ABI STREQUAL "armeabi-v7a")
        set(OpenCV_DIR_Android "${OpenCV_DIR}/armeabi-v7a")
    endif ()

    set(OpenCV_LIBS "")
    set(LIB_MNN "${OpenCV_DIR_Android}/libMNN.so")
    set(LIB_opencv_core "${OpenCV_DIR_Android}/libopencv_core.so")
    set(LIB_opencv_dnn "${OpenCV_DIR_Android}/libopencv_dnn.so")
    set(LIB_opencv_imgproc "${OpenCV_DIR_Android}/libopencv_imgproc.so")
    set(LIB_ANDROID_LOG "")
    if(ANDROID)
        find_library(log-lib log)
        message(STATUS "Found android log lib at ${log-lib}!")
    endif()

    if (NOT LIB_opencv_core)
        message(SEND_ERROR "Cound not found opencv_core from ${OpenCV_DIR_Android}")
    endif ()

    list(APPEND OpenCV_LIBS ${LIB_MNN})
    list(APPEND OpenCV_LIBS ${LIB_opencv_core})
    list(APPEND OpenCV_LIBS ${LIB_opencv_dnn})
    list(APPEND OpenCV_LIBS ${LIB_opencv_imgproc})
    list(APPEND OpenCV_LIBS ${log-lib})
else ()
    find_package(OpenCV REQUIRED)
    if (OpenCV_FOUND)
        message(STATUS "Found OpenCV Lib at ${OpenCV_DIR}!")
        include_directories( ${OpenCV_INCLUDE_DIRS})
    else ()
        message(SEND_ERROR "Cound nont found OpenCV lib at ${OpenCV_DIR}")
    endif ()
endif ()


#mpp_vision face detector
if(MPP_BUILD_FACE_DETECTOR)
    include(${CMAKE_CURRENT_SOURCE_DIR}/face_detector/CMakeLists.txt)
endif()

#mpp_vision face landmarker
if(MPP_BUILD_FACE_LANDMARKER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/face_landmarker/CMakeLists.txt)
endif()

#mpp_vision hand detector
if(MPP_BUILD_HAND_DETECTOR)
    include(${CMAKE_CURRENT_SOURCE_DIR}/hand_detector/CMakeLists.txt)
endif()

#mpp_vision hand landmark
if(MPP_BUILD_HAND_LANDMARKER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/hand_landmarker/CMakeLists.txt)
endif()

#mpp_vision pose detector
if(MPP_BUILD_POSE_DETECTOR)
    include(${CMAKE_CURRENT_SOURCE_DIR}/pose_detector/CMakeLists.txt)
endif()

#mpp_vision pose landmark
if(MPP_BUILD_POSE_LANDMARKER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/pose_landmarker/CMakeLists.txt)
endif()

#mpp_vision interactive_segmenter
if(MPP_BUILD_INTERACTIVE_SEGMENTER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/interactive_segmenter/CMakeLists.txt)
endif()

#mpp_vision hair_segmenter
if(MPP_BUILD_HAIR_SEGMENTER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/hair_segmenter/CMakeLists.txt)
endif()

#mpp_vision selfie_segmenter
if(MPP_BUILD_SELFIE_SEGMENTER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/selfie_segmenter/CMakeLists.txt)
endif()

#mpp_vision iris_tracker
if(MPP_BUILD_IRIS_TRACKER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/iris_tracker/CMakeLists.txt)
endif()

#mpp_vision holistic tracker
if(MPP_BUILD_HOLISTIC_TRACKER)
    include(${CMAKE_CURRENT_SOURCE_DIR}/holistic_tracker/CMakeLists.txt)
endif()
