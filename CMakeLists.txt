cmake_minimum_required (VERSION 3.12)
project (PE-bear_full)

# modules:
set ( M_SIGFIND "sig_finder/sig_finder" )
set ( M_BEARPARSER "bearparser/parser" )
set ( M_DISASM "disasm" )
set ( M_CAPSTONE "capstone" )

option(USE_QT4 "Use Qt4" OFF )
option(USE_QT5 "Use Qt5" OFF )
option(SHOW_CONSOLE "Show debug console" OFF )

# libraries should be searched in a local directory of the binary
if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")

	if(RELATIVE_LIBS)
		set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,-rpath='$ORIGIN'")
	endif()

endif()

# modules paths:
set (PARSER_DIR "${CMAKE_SOURCE_DIR}/${M_BEARPARSER}" CACHE PATH "BearParser main path")
set (PARSER_INC "${PARSER_DIR}/include" CACHE PATH "BearParser include path")

set( SIGFIND_DIR "${CMAKE_SOURCE_DIR}/${M_SIGFIND}" CACHE PATH "SigFinder main path")
set (SIGFIND_INC "${SIGFIND_DIR}/include" CACHE PATH "SigFinder include path")

set( DISASM_DIR "${CMAKE_SOURCE_DIR}/${M_DISASM}" CACHE PATH "Disasm main path")

set (CAPSTONE_DIR "${CMAKE_SOURCE_DIR}/${M_CAPSTONE}" CACHE PATH "Capstone main path")
set (CAPSTONE_INC "${CAPSTONE_DIR}/include" CACHE PATH "Capstone include path")

#capstone settings:
option(CAPSTONE_INSTALL "Install Capstone" OFF)
option(CAPSTONE_BUILD_STATIC_RUNTIME "Embed static runtime" OFF)
option(CAPSTONE_BUILD_STATIC "Build static library" ON)
option(CAPSTONE_BUILD_SHARED "Build shared library" OFF)
option(CAPSTONE_BUILD_DIET "Build diet library" OFF)
option(CAPSTONE_BUILD_TESTS "Build tests" OFF)
option(CAPSTONE_USE_DEFAULT_ALLOC "Use default memory allocation functions" ON)
option(CAPSTONE_ARM_SUPPORT "ARM support" ON)
option(CAPSTONE_ARM64_SUPPORT "ARM64 support" ON)
option(CAPSTONE_MIPS_SUPPORT "MIPS support" OFF)
option(CAPSTONE_PPC_SUPPORT "PowerPC support" OFF)
option(CAPSTONE_SPARC_SUPPORT "Sparc support" OFF)
option(CAPSTONE_SYSZ_SUPPORT "SystemZ support" OFF)
option(CAPSTONE_XCORE_SUPPORT "XCore support" OFF)
option(CAPSTONE_TRICORE_SUPPORT "TriCore support" OFF)

option(CAPSTONE_M68K_SUPPORT "M68K support" OFF)
option(CAPSTONE_MOS65XX_SUPPORT "MOS65XX support" OFF)
option(CAPSTONE_M680X_SUPPORT "M680X support" OFF)
option(CAPSTONE_TMS320C64X_SUPPORT "TMS320C64X support" OFF)
option(CAPSTONE_EVM_SUPPORT "EVM support" OFF)
option(CAPSTONE_WASM_SUPPORT "WASM support" OFF)
option(CAPSTONE_BPF_SUPPORT "BPF support" OFF)
option(CAPSTONE_RISCV_SUPPORT "RISCV support" OFF)
 
option(CAPSTONE_X86_SUPPORT "x86 support" ON)
option(CAPSTONE_X86_REDUCE "x86 with reduce instruction sets to minimize library" OFF)
option(CAPSTONE_X86_ATT_DISABLE "Disable x86 AT&T syntax" ON)
option(CAPSTONE_SH_SUPPORT "SH support" OFF)

if(CAPSTONE_ARM64_SUPPORT)
	add_compile_definitions(USE_ARM64)
endif()

if(CAPSTONE_ARM_SUPPORT)
	add_compile_definitions(USE_ARM32)
endif()

# Add sub-directories
#
# libs

add_subdirectory(${M_CAPSTONE})
set(CAPSTONE_LIB $<TARGET_FILE:capstone> CACHE PATH "Capstone library path")

add_subdirectory(${M_BEARPARSER})
set(PARSER_LIB $<TARGET_FILE:bearparser> CACHE PATH "BearParser library path")

add_subdirectory(${M_DISASM})
set(DISASM_LIB $<TARGET_FILE:libdisasm> CACHE PATH "Disasm library path")

add_subdirectory(${M_SIGFIND})
set(SIGFIND_LIB $<TARGET_FILE:sig_finder> CACHE PATH "SigFinder library path")

# executables
add_subdirectory(pe-bear)

add_dependencies(libdisasm bearparser capstone)
add_dependencies(PE-bear bearparser sig_finder libdisasm capstone)

