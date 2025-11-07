
add_library(yara-interface INTERFACE)
target_include_directories(yara-interface INTERFACE "libyara/include")

add_library(yara-private-interface INTERFACE)
target_include_directories(yara-private-interface INTERFACE "libyara")


clock_sources(sources
    "${CMAKE_CURRENT_SOURCE_DIR}/libyara"
    "${CMAKE_CURRENT_SOURCE_DIR}/libyara/modules/tests"
    "${CMAKE_CURRENT_SOURCE_DIR}/libyara/modules/pe"
    "${CMAKE_CURRENT_SOURCE_DIR}/libyara/modules/elf"
    "${CMAKE_CURRENT_SOURCE_DIR}/libyara/modules/math"
    "${CMAKE_CURRENT_SOURCE_DIR}/libyara/modules/time"
    "${CMAKE_CURRENT_SOURCE_DIR}/libyara/modules/console"
)

if ( CLOCK_TARGET_IS_LINUX )
    LIST(APPEND sources ${CMAKE_CURRENT_SOURCE_DIR}/libyara/proc/linux.c)
endif()

if ( CLOCK_TARGET_IS_MACOS )
    LIST(APPEND sources ${CMAKE_CURRENT_SOURCE_DIR}/libyara/proc/mach.c)
endif()

if ( CLOCK_TARGET_IS_WINDOWS )
    LIST(APPEND sources ${CMAKE_CURRENT_SOURCE_DIR}/libyara/proc/windows.c)
endif()

add_library(yara-static STATIC ${sources})

target_link_libraries(yara-static
    PUBLIC  yara-interface
    PRIVATE yara-private-interface
)

if ( CLOCK_TARGET_IS_WINDOWS )
    target_compile_definitions(yara-static PRIVATE USE_WINDOWS_PROC)
endif()

if ( CLOCK_TARGET_IS_LINUX )
    target_compile_definitions(yara-static PRIVATE USE_LINUX_PROC)
endif()

if ( CLOCK_TARGET_IS_MACOS )
    target_compile_definitions(yara-static PRIVATE USE_MACH_PROC)
endif()

if ( CLOCK_COMPILER_IS_CLANG )
    target_compile_options(yara-static  PRIVATE "-Wno-tautological-constant-out-of-range-compare")
    target_compile_options(yara-static  PRIVATE "-Wno-deprecated-declarations")
    target_compile_options(yara-static  PRIVATE "-Wno-tautological-pointer-compare")
    target_compile_options(yara-static  PRIVATE "-Wno-unused-function")
    target_compile_options(yara-static  PRIVATE "-Wno-unused-but-set-variable")

endif()

if ( CLOCK_COMPILER_IS_MSVC )
    target_compile_options(yara-static PRIVATE "/wd4244") # warning C4244: '=': conversion from 'uint32_t' to 'uint8_t', possible loss of data
    target_compile_options(yara-static PRIVATE "/wd4324") # warning C4324: '_YR_COMPILER': structure was padded due to alignment specifier
    target_compile_options(yara-static PRIVATE "/wd4702") # warning C4702: unreachable code
    target_compile_options(yara-static PRIVATE "/wd4701") # warning C4701: potentially uninitialized local variable 'r1' used
    target_compile_options(yara-static PRIVATE "/wd4146") # warning C4146: unary minus operator applied to unsigned type, result still unsigned
    target_compile_options(yara-static PRIVATE "/wd4100") # warning C4100: 'calling_rule_filename': unreferenced formal parameter
    target_compile_options(yara-static PRIVATE "/wd4267") # warning C4267: 'function': conversion from 'size_t' to 'DWORD', possible loss of datawarning C4100: 'calling_rule_filename': unreferenced formal parameter
    target_compile_options(yara-static PRIVATE "/wd4127") # warning C4127: conditional expression is constant
    target_compile_options(yara-static PRIVATE "/wd4456") # warning C4456: declaration of 'i' hides previous local declaration
    target_compile_options(yara-static PRIVATE "/wd4389") # warning C4389: '!=': signed/unsigned mismatch
    target_compile_options(yara-static PRIVATE "/wd4996") # warning C4996: 'read': The POSIX name for this item is deprecated
    target_compile_options(yara-static PRIVATE "/wd4018") # warning C4018: '>': signed/unsigned mismatch
    target_compile_options(yara-static PRIVATE "/wd4245") # warning C4245: 'function': conversion from '__int64' to 'uint64_t', signed/unsigned mismatch
    target_compile_options(yara-static PRIVATE "/wd4200") # warning C4200: nonstandard extension used: zero-sized array in struct/union
    target_compile_options(yara-static PRIVATE "/wd4130") # warning C4130: '==': logical operation on address of string constant
    target_compile_options(yara-static PRIVATE "/wd4211") # warning C4211: nonstandard extension used: redefined extern to static
    target_compile_options(yara-static PRIVATE "/wd4310") # warning C4310: cast truncates constant value
    target_compile_options(yara-static PRIVATE "/wd4305") # warning C4305: 'type cast': truncation from '__int64' to 'char *'
endif()

if ( CLOCK_COMPILER_IS_GCC )
    target_compile_options(yara-static PRIVATE "-Wno-stringop-overflow")
    target_compile_options(yara-static PRIVATE "-Wno-nonnull-compare")
endif()
