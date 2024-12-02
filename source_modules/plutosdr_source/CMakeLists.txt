cmake_minimum_required(VERSION 3.13)
project(plutosdr_source)

file(GLOB SRC "src/*.cpp")

include(${SDRPP_MODULE_CMAKE})

if (MSVC)
    # Lib path
    target_link_directories(plutosdr_source PRIVATE "C:/Program Files/PothosSDR/lib/")
    target_include_directories(plutosdr_source PRIVATE "C:/Program Files/PothosSDR/include/")

    target_link_libraries(plutosdr_source PRIVATE libiio)
    target_link_libraries(plutosdr_source PRIVATE libad9361)
elseif (ANDROID)
    find_package(ad9361)
    find_package(libxml2)
    find_package(libiio)
    target_link_libraries(plutosdr_source PUBLIC
        LibXml2::LibXml2
        iio
        ad9361
    )
else (MSVC)
    if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
        target_include_directories(plutosdr_source PRIVATE "/Library/Frameworks/iio.framework/Headers")
        target_link_libraries(plutosdr_source PRIVATE "/Library/Frameworks/iio.framework/iio")

        target_include_directories(plutosdr_source PRIVATE "/Library/Frameworks/ad9361.framework/Headers")
        target_link_libraries(plutosdr_source PRIVATE "/Library/Frameworks/ad9361.framework/ad9361")
    else()
        find_package(PkgConfig)

        pkg_check_modules(LIBIIO REQUIRED libiio)
        pkg_check_modules(LIBAD9361 REQUIRED libad9361)

        target_include_directories(plutosdr_source PRIVATE ${LIBIIO_INCLUDE_DIRS})
        target_link_directories(plutosdr_source PRIVATE ${LIBIIO_LIBRARY_DIRS})
        target_link_libraries(plutosdr_source PRIVATE ${LIBIIO_LIBRARIES})

        target_include_directories(plutosdr_source PRIVATE ${LIBAD9361_INCLUDE_DIRS})
        target_link_directories(plutosdr_source PRIVATE ${LIBAD9361_LIBRARY_DIRS})
        target_link_libraries(plutosdr_source PRIVATE ${LIBAD9361_LIBRARIES})
    endif()

    target_include_directories(plutosdr_source PRIVATE ${LIBAD9361_INCLUDE_DIRS})
endif ()
