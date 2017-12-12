cmake_minimum_required(VERSION 3.3)

project(OpenCFLite VERSION 635.21 LANGUAGES CXX)

include(GNUInstallDirs)

find_package(ICU REQUIRED COMPONENTS data i18n uc)

include(FindPackageHandleStandardArgs)
find_path(PTHREAD_W32_INCLUDE_DIRS pthread.h)
find_library(PTHREAD_W32_LIBRARIES pthreadsVC2)
find_package_handle_standard_args(PTHREAD_W32 DEFAULT_MSG PTHREAD_W32_INCLUDE_DIRS PTHREAD_W32_LIBRARIES)

add_definitions(-DUNICODE -D_UNICODE)

# for Windows, dll should be in the same directory with exe files
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/${CMAKE_CFG_INTDIR}/bin)

option(BUILD_SHARED_LIBS "Create shared libraries" ON)

add_library(CFLite
    CFApplicationPreferences.c
    CFArray.c
    CFBag.c
    CFBase.c
    CFBasicHash.c
    CFBigNumber.c
    CFBinaryHeap.c
    CFBinaryPList.c
    CFBitVector.c
    CFBuiltinConverters.c
    CFBundle.c
    CFBundle_Resources.c
    CFBurstTrie.c
    CFCalendar.c
    CFCharacterSet.c
    CFConcreteStreams.c
    CFData.c
    CFDate.c
    CFDateFormatter.c
    CFDictionary.c
    CFError.c
    CFFileDescriptor.c
    CFFileUtilities.c
    CFICUConverters.c
    CFLocale.c
    CFLocaleIdentifier.c
    CFLocaleKeys.c
    CFMachPort.c
    CFMessagePort.c
    CFNotificationCenter.c
    CFNumber.c
    CFNumberFormatter.c
    CFOldStylePList.c
    CFPlatform.c
    CFPlatformConverters.c
    CFPlugIn.c
    CFPlugIn_Factory.c
    CFPlugIn_Instance.c
    CFPlugIn_PlugIn.c
    CFPreferences.c
    CFPropertyList.c
    CFRunLoop.c
    CFRuntime.c
    CFSet.c
    CFSocket.c
    CFSocketStream.c
    CFSortFunctions.c
    CFStorage.c
    CFStream.c
    CFString.c
    CFStringEncodingConverter.c
    CFStringEncodingDatabase.c
    CFStringEncodings.c
    CFStringScanner.c
    CFStringUtilities.c
    CFSystemDirectories.c
    CFTimeZone.c
    CFTree.c
    CFURL.c
    CFURLAccess.c
    CFUUID.c
    CFUniChar.c
    CFUnicodeDecomposition.c
    CFUnicodePrecomposition.c
    CFUserNotification.c
    CFUtilities.c
    CFVersion.c
    CFWindowsMessageQueue.c
    CFWindowsUtilities.c
    CFXMLInputStream.c
    CFXMLNode.c
    CFXMLParser.c
    CFXMLPreferencesDomain.c
    CFXMLTree.c
    compat/dirent.c
    compat/gettimeofday.c
    compat/strlcat.c
    compat/strlcpy.c
)

set(build_include_dir ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/include)
file(GLOB cf_header_files *.h)

add_custom_target(CFLitePre
    COMMAND ${CMAKE_COMMAND} -E make_directory ${build_include_dir}
    COMMAND ${CMAKE_COMMAND} -E make_directory ${build_include_dir}/CoreFoundation
    COMMAND ${CMAKE_COMMAND} -E copy_if_different ${cf_header_files} ${build_include_dir}/CoreFoundation
    VERBATIM
)
add_dependencies(CFLite CFLitePre)

target_include_directories(CFLite
    PRIVATE
    ${PTHREAD_W32_INCLUDE_DIRS}
    ${build_include_dir}
    include
    include/c99
)

target_compile_definitions(CFLite
    PRIVATE
    CF_BUILDING_CF
    DEPLOYMENT_TARGET_WINDOWS
    __STDC_LIMIT_MACROS
)

target_link_libraries(CFLite
    PRIVATE
    ${PTHREAD_W32_LIBRARIES}
    ICU::data
    ICU::i18n
    ICU::uc
    rpcrt4.lib
    ws2_32.lib
    wsock32.lib
)

get_property(src TARGET CFLite PROPERTY SOURCES)
list(FILTER src INCLUDE REGEX "\\.c$")
set_source_files_properties(${src} PROPERTIES LANGUAGE CXX)

install(TARGETS CFLite
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(FILES
    include/AssertMacros.h
    include/Availability.h
    include/AvailabilityInternal.h
    include/AvailabilityMacros.h
    include/ConditionalMacros.h
    include/TargetConditionals.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(FILES
    CFArray.h
    CFBag.h
    CFBase.h
    CFBasicHash.h
    CFBigNumber.h
    CFBinaryHeap.h
    CFBitVector.h
    CFBundle.h
    CFBurstTrie.h
    CFByteOrder.h
    CFCalendar.h
    CFCharacterSet.h
    CFData.h
    CFDate.h
    CFDateFormatter.h
    CFDictionary.h
    CFError.h
    CFFileDescriptor.h
    CFICUConverters.h
    CFLocale.h
    CFLocaleInternal.h
    CFLogUtilities.h
    CFMachPort.h
    CFMessagePort.h
    CFNotificationCenter.h
    CFNumber.h
    CFNumberFormatter.h
    CFPlugIn.h
    CFPlugInCOM.h
    CFPreferences.h
    CFPropertyList.h
    CFRunLoop.h
    CFSet.h
    CFSocket.h
    CFStream.h
    CFString.h
    CFStringEncodingDatabase.h
    CFStringEncodingExt.h
    CFTimeZone.h
    CFTree.h
    CFURL.h
    CFURLAccess.h
    CFURLPriv.h
    CFUserNotification.h
    CFUUID.h
    CFWindowsMessageQueue.h
    CFXMLInputStream.h
    CFXMLNode.h
    CFXMLParser.h
    CoreFoundation.h
    CoreFoundation_Prefix.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/CoreFoundation)
