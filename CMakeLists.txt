CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

PROJECT(JSONSpirit)
if (NOT CMAKE_CXX_VERSION)
    message(STATUS "json_spirit is defaulting to c++14")
    set(CMAKE_CXX_VERSION 14)
endif ()
SUBDIRS(json_spirit json_demo json_headers_only_demo json_map_demo json_test)
INCLUDE_DIRECTORIES(json_spirit)

add_custom_target(install_${PROJECT_NAME}
        $(MAKE) install
        DEPENDS json_spirit
        COMMENT "Installing ${PROJECT_NAME}")
