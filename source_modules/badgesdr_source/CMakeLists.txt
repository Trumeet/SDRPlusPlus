cmake_minimum_required(VERSION 3.13)
project(badgesdr_source)

file(GLOB SRC "src/*.cpp")

include(${SDRPP_MODULE_CMAKE})

if (MSVC)
    find_package(libusb CONFIG REQUIRED)
    target_include_directories(badgesdr_source PRIVATE ${LIBUSB_INCLUDE_DIRS})
    target_link_libraries(badgesdr_source PRIVATE ${LIBUSB_LIBRARIES})
elseif (ANDROID)
    find_package(libusb)
    find_package(rtlsdr)
    target_link_libraries(badgesdr_source PUBLIC
        libusb
        rtlsdr::rtlsdr_static
    )
else (MSVC)
    find_package(PkgConfig)

    pkg_check_modules(LIBUSB REQUIRED libusb-1.0)

    target_include_directories(badgesdr_source PRIVATE ${LIBUSB_INCLUDE_DIRS})
    target_link_directories(badgesdr_source PRIVATE ${LIBUSB_LIBRARY_DIRS})
    target_link_libraries(badgesdr_source PRIVATE ${LIBUSB_LIBRARIES})
endif ()
