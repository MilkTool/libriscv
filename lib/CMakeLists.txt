#
# C++17 RISC-V emulator library
#

option(RISCV_DEBUG  "Enable debugging features in the RISC-V machine" OFF)
option(RISCV_ICACHE "Enable instruction decoder cache" ON)
option(RISCV_EXT_A  "Enable RISC-V atomic instructions" ON)
option(RISCV_EXT_C  "Enable RISC-V compressed instructions" ON)
option(RISCV_EXT_F  "Enable RISC-V floating-point instructions" ON)
option(RISCV_EXPERIMENTAL  "Enable experimental features" OFF)

set (SOURCES
		libriscv/cpu.cpp
		libriscv/decoder_cache.cpp
		libriscv/machine.cpp
		libriscv/memory.cpp
		libriscv/memory_rw.cpp
		libriscv/rv32i.cpp
		libriscv/rv64i.cpp
		libriscv/serialize.cpp
	)
if (RISCV_DEBUG)
	list(APPEND SOURCES
		libriscv/debug.cpp
	)
endif()

add_subdirectory(EASTL)

add_library(riscv ${SOURCES})
set_target_properties(riscv PROPERTIES CXX_STANDARD 17)
target_include_directories(riscv PUBLIC .)
target_link_libraries(riscv EASTL)
target_compile_options(riscv PRIVATE -Wall -Wextra)
target_compile_options(riscv PUBLIC -Wno-deprecated-copy)
if (RISCV_DEBUG)
	target_compile_definitions(riscv PUBLIC RISCV_DEBUG=1)
endif()
if (RISCV_EXT_A)
	target_compile_definitions(riscv PUBLIC RISCV_EXT_ATOMICS=1)
endif()
if (RISCV_EXT_C)
	target_compile_definitions(riscv PUBLIC RISCV_EXT_COMPRESSED=1)
endif()
if (RISCV_EXT_F)
	target_compile_definitions(riscv PUBLIC RISCV_EXT_FLOATS=1)
endif()
if (RISCV_ICACHE)
	target_compile_definitions(riscv PUBLIC RISCV_INSTR_CACHE=1)
endif()
if (RISCV_EXPERIMENTAL)
	target_compile_definitions(riscv PUBLIC
		RISCV_INSTR_CACHE_PREGEN=1)
endif()
