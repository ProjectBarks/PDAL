#
# MrSID plugin CMake configuration
#

set(MRSID_ROOT "/Users/hobu/installs/mrsid/Lidar_DSDK" CACHE STRING "Root directory of MrSID install")
find_package(MrSID)
if (MRSID_FOUND)
    PDAL_ADD_PLUGIN(reader_libname reader mrsid
        FILES
            io/MrsidReader.cpp
        LINK_WITH ${MRSID_LIBRARY})
    target_include_directories(${reader_libname} PRIVATE
        ${PDAL_IO_DIR}
        ${MRSID_INCLUDE_DIR})
    target_compile_definitions(${reader_libname} PRIVATE -DHAVE_MRSID=1)
    if (CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR CMAKE_CXX_COMPILER_ID MATCHES "GNU" )
        set_source_files_properties(io/MrsidReader.cpp PROPERTIES COMPILE_FLAGS -Wno-pedantic)
    endif()

    if (WITH_TESTS)
        PDAL_ADD_TEST(mrsidtest
            FILES test/MrsidTest.cpp
            LINK_WITH ${reader_libname})
        target_include_directories(mrsidtest PRIVATE
            ${PDAL_IO_DIR})
    endif()
else()
    message(STATUS "Building without MrSID support")
endif()
