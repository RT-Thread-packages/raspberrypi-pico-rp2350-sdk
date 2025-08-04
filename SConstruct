import os
import re
import shutil

PREFIX = 'arm-none-eabi-'
EXEC_PATH = ''
if os.getenv('RTT_EXEC_PATH'):
    EXEC_PATH = os.getenv('RTT_EXEC_PATH')

env = Environment(
CC = PREFIX + 'gcc',
AS = PREFIX + 'gcc',
AR = PREFIX + 'ar', ARFLAGS = '-rc',
CXX = PREFIX + 'g++',
LINK = PREFIX + 'gcc',
TARGET_EXT = 'elf',
SIZE = PREFIX + 'size',
OBJDUMP = PREFIX + 'objdump',
OBJCPY = PREFIX + 'objcopy',
CCFLAGS=Split('''
-mcpu=cortex-m33
-mthumb
-march=armv8-m.main+fp+dsp 
-mfloat-abi=softfp
-mcmse
-g
-O3 
-Wl,--build-id=none 
--specs=nosys.specs 
-nostartfiles
-Wl,--script=src/rp2350/boot_stage2/boot_stage2.ld
-Wl,-Map=bs2_default.elf.map
'''),
LINKFLAGS=Split('''
-mcpu=cortex-m33
-mthumb
-march=armv8-m.main+fp+dsp 
-mfloat-abi=softfp
-mcmse
-g
-O3 
--specs=nosys.specs 
-nostartfiles
-Wl,--script=src/rp2350/boot_stage2/boot_stage2.ld
'''),
CPPDEFINES = Split('''
LIB_BOOT_STAGE2_HEADERS=1
PICO_32BIT=1
PICO_BUILD=1
PICO_NO_HARDWARE=0
DPICO_ON_DEVICE=1
PICO_RP2350=1
'''),
CPPPATH = Split('''
src/rp2350/boot_stage2/asminclude
src/rp2350/hardware_regs/include
src/rp2_common/hardware_base/include 
src/common/pico_base_headers/include
src/boards/include
src/rp2350/pico_platform/include
src/rp2_common/pico_platform_compiler/include
src/rp2_common/pico_platform_panic/include
src/rp2_common/pico_platform_sections/include
src/rp2_common/hardware_dcp/include
src/rp2350/hardware_structs/include
src/rp2_common/hardware_rcp/include 
src/boards/include
src/rp2350/boot_stage2/include
src/rp2_common/cmsis/include
''')
)
env.PrependENVPath('PATH', EXEC_PATH)

env.Program(
    'bs2_default.elf',
    ['src/rp2350/boot_stage2/compile_time_choice.S'],
)

env.Command(
    'bs2_default.bin',
    'bs2_default.elf',
    env['OBJCPY'] + ' -Obinary bs2_default.elf bs2_default.bin',
)

env.Command(
    'bs2_default_padded_checksummed.S',
    'bs2_default.bin',
    'python3 src/rp2350/boot_stage2/pad_checksum -s 0xffffffff bs2_default.bin bs2_default_padded_checksummed.S',
)