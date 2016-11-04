set(JSON_SPIRIT_PUBLIC_INTERFACE json_spirit.hpp)

set(JSON_SPIRIT_PRIVATE_INTERFACE
        json_spirit/json_spirit.h
        json_spirit/json_spirit_error_position.h
        json_spirit/json_spirit_reader.h
        json_spirit/json_spirit_reader_template.h
        json_spirit/json_spirit_stream_reader.h
        json_spirit/json_spirit_utils.h
        json_spirit/json_spirit_value.h
        json_spirit/json_spirit_writer.h
        json_spirit/json_spirit_writer_template.h
        json_spirit/json_spirit_writer_options.h)

SET(JSON_SPIRIT_SRCS
        src/json_spirit_reader.cpp json_spirit/json_spirit_reader.h
        src/json_spirit_value.cpp json_spirit/json_spirit_value.h
        src/json_spirit_writer.cpp json_spirit/json_spirit_writer.h
        ${JSON_SPIRIT_PUBLIC_INTERFACE}
        ${JSON_SPIRIT_PRIVATE_INTERFACE})

 
FIND_PACKAGE(Boost 1.34 REQUIRED)

ADD_LIBRARY(json_spirit STATIC ${JSON_SPIRIT_SRCS})
TARGET_INCLUDE_DIRECTORIES(json_spirit SYSTEM PUBLIC ${Boost_INCLUDE_DIR})
TARGET_INCLUDE_DIRECTORIES(json_spirit SYSTEM PUBLIC $<INSTALL_INTERFACE:include>)

set_target_properties(json_spirit PROPERTIES PUBLIC_HEADER "${JSON_SPIRIT_PUBLIC_INTERFACE}")
set_target_properties(json_spirit PROPERTIES PRIVATE_HEADER "${JSON_SPIRIT_PRIVATE_INTERFACE}")

set(JSONSpirit_MAJOR_VERSION 4)
set(JSONSpirit_MINOR_VERSION 08)
set(JSONSpirit_PATCH_VERSION 0)
set(JSONSpirit_VERSION
        ${JSONSpirit_MAJOR_VERSION}.${JSONSpirit_MINOR_VERSION}.${JSONSpirit_PATCH_VERSION})

set(INSTALL_LIB_DIR lib CACHE PATH "Installation directory for libraries")
set(INSTALL_BIN_DIR bin CACHE PATH "Installation directory for executables")
set(INSTALL_INCLUDE_DIR include CACHE PATH "Installation directory for header files")
if(WIN32 AND NOT CYGWIN)
    set(DEF_INSTALL_CMAKE_DIR CMake)
else()
    set(DEF_INSTALL_CMAKE_DIR lib/cmake/JSONSpirit)
endif()
set(INSTALL_CMAKE_DIR ${DEF_INSTALL_CMAKE_DIR} CACHE PATH "Installation directory for CMake files")

# Make relative paths absolute (needed later on)
foreach(p LIB BIN INCLUDE CMAKE)
    set(var INSTALL_${p}_DIR)
    if(NOT IS_ABSOLUTE "${${var}}")
        set(${var} "${CMAKE_INSTALL_PREFIX}/${${var}}")
    endif()
endforeach()

install(TARGETS json_spirit
        # IMPORTANT: Add the foo library to the "export-set"
        EXPORT JSONSpiritTargets
        RUNTIME DESTINATION "${INSTALL_BIN_DIR}" COMPONENT bin
        LIBRARY DESTINATION "${INSTALL_LIB_DIR}" COMPONENT shlib
        ARCHIVE DESTINATION "${INSTALL_LIB_DIR}" COMPONENT lib
        PUBLIC_HEADER DESTINATION "${INSTALL_INCLUDE_DIR}"
        PRIVATE_HEADER DESTINATION "${INSTALL_INCLUDE_DIR}/json_spirit"
        COMPONENT dev)
export(TARGETS json_spirit FILE JSONSpiritTargets.cmake)
export(PACKAGE JSONSpirit)

file(RELATIVE_PATH REL_INCLUDE_DIR "${INSTALL_CMAKE_DIR}" "${INSTALL_INCLUDE_DIR}")
set(CONF_INCLUDE_DIRS "${PROJECT_SOURCE_DIR}" "${PROJECT_BINARY_DIR}")
configure_file(JSONSpiritConfig.cmake.in ${PROJECT_BINARY_DIR}/JSONSpiritConfig.cmake @ONLY)

# ... for the install tree
set(CONF_INCLUDE_DIRS "\${JSONSpirit_CMAKE_DIR}/${REL_INCLUDE_DIR}")
configure_file(JSONSpiritConfig.cmake.in "${PROJECT_BINARY_DIR}/${CMAKE_FILES_DIRECTORY}/JSONSpiritConfig.cmake" @ONLY)

# ... for both
configure_file(JSONSpiritConfigVersion.cmake.in "${PROJECT_BINARY_DIR}/JSONSpiritConfigVersion.cmake" @ONLY)

# Install the JSONSpiritConfig.cmake and JSONSpiritConfigVersion.cmake
install(FILES
        "${PROJECT_BINARY_DIR}/${CMAKE_FILES_DIRECTORY}/JSONSpiritConfig.cmake"
        "${PROJECT_BINARY_DIR}/JSONSpiritConfigVersion.cmake"
        DESTINATION "${INSTALL_CMAKE_DIR}" COMPONENT dev)

# Install the export set for use with the install-tree
install(EXPORT JSONSpiritTargets DESTINATION
        "${INSTALL_CMAKE_DIR}" COMPONENT dev)

