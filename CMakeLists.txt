cmake_minimum_required(VERSION 2.8)

project(deep_sort)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++14" COMPILER_SUPPORTS_CXX14)

if(COMPILER_SUPPORTS_CXX14)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -pthread")
else()
  message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()


find_package(OpenCV REQUIRED)

message(STATUS "OpenCV library status:")
message(STATUS "    version: ${OpenCV_VERSION}")
message(STATUS "    libraries: ${OpenCV_LIBS}")
message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")

find_package(OpenMP REQUIRED)
if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

find_package(CUDA)
find_package(Eigen3 REQUIRED)
find_package(Boost REQUIRED system serialization)



include_directories(${EIGEN3_INCLUDE_DIR})
include_directories(${OpenCV_INCLUDE_DIRS})
# include_directories(${Boost_INCLUDE_DIRS})

include_directories(${CMAKE_CURRENT_LIST_DIR}/src/matching)
include_directories(${CMAKE_CURRENT_LIST_DIR}/src/3rdparty)
include_directories(${CMAKE_CURRENT_LIST_DIR}/src/features)
include_directories(${CMAKE_CURRENT_LIST_DIR}/src/app)

add_definitions(-Wuninitialized)
add_definitions(-Wreturn-type)
add_definitions(-Wsign-compare)
add_definitions(-Wuninitialized)
add_definitions(-Wunused-function)
add_definitions(-Wunused-label)    
add_definitions(-Wunused-value) 
add_definitions(-Wunused-variable)

set(DEEPSORT_HEADERS
	
		src/3rdparty/adapter.h
		src/3rdparty/boostmatrixadapter.h
		src/3rdparty/hungarianoper.h
		src/3rdparty/matrix.h
		src/3rdparty/munkres.h

		src/matching/kalmanfilter.h
		src/matching/nn_matching.h
		src/matching/tracker.h
		src/matching/linear_assignment.h
		src/matching/track.h

		src/features/FeatureTensor.h
		src/features/model.h
		src/features/dtaType.h

		src/app/errmsg.h
		src/app/param.h
		src/app/VideoTracker.h
		src/app/utility.h)



set(DEEPSORT_SRC

		src/3rdparty/adapter.cpp
		src/3rdparty/boostmatrixadapter.cpp
		src/3rdparty/hungarianoper.cpp
		src/3rdparty/munkres.cpp

		src/matching/kalmanfilter.cpp
		src/matching/nn_matching.cpp
		src/matching/tracker.cpp
		src/matching/linear_assignment.cpp
		src/matching/track.cpp

		src/features/FeatureTensor.cpp
		src/features/model.cpp

		src/app/errmsg.cpp
		src/app/param.cpp
		src/app/VideoTracker.cpp)


# Library target
add_library(deep_sort SHARED ${DEEPSORT_SRC})


target_link_libraries(deep_sort PRIVATE ${CUDA_LIBRARIES})
target_link_libraries(deep_sort PRIVATE ${OpenCV_LIBS})
target_link_libraries(deep_sort PRIVATE Boost::system Boost::serialization)

find_package(TensorflowCC REQUIRED)
target_link_libraries(deep_sort PRIVATE TensorflowCC::TensorflowCC)


set(DEEPSORT_INCLUDE_DIRS 

		${CMAKE_CURRENT_LIST_DIR}/src/matching 
		${CMAKE_CURRENT_LIST_DIR}/src/3rdparty
		${CMAKE_CURRENT_LIST_DIR}/src/features
		${CMAKE_CURRENT_LIST_DIR}/src/app

    CACHE INTERNAL "${PROJECT_NAME}: Include Directories" FORCE)
