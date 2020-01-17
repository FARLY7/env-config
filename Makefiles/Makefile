######################################
# Windows & Linux Compatibility
######################################
ifeq ($(OS),Windows_NT)
  ifeq ($(shell uname -s),) # not in a bash-like shell
	CLEANUP = del /F /Q
	MKDIR = mkdir
  else # in a bash-like shell, like msys
	CLEANUP = rm -rf
	MKDIR = mkdir -p
  endif
else
	CLEANUP = rm -rf
	MKDIR = mkdir -p
endif

######################################
# Target
######################################
TARGET = output

######################################
# Building variables
######################################
# Debug build?
DEBUG = 1
# optimization
OPT = -Og

#######################################
# Paths
#######################################
# Build path
BUILD_DIR = build

#######################################
# Git Version Information
#######################################
version 		:= $(subst -, ,$(shell git describe --long --dirty --tags))
COMMIT 			:= $(strip $(word 3, $(version)))
COMMITS_PAST 	:= $(strip $(word 2, $(version)))
DIRTY 			:= $(strip $(word 4, $(version)))

ifneq ($(COMMITS_PAST), 0)
    BUILD_INFO_COMMITS := "."$(COMMITS_PAST)
endif
ifneq ($(DIRTY),)
    BUILD_INFO_DIRTY :="+"
endif

export MAGIC_STRING  := "@{\#}"
export BUILD_TAG	 := $(strip $(word 1, $(version)))
export BUILD_INFO 	 := $(COMMIT)$(BUILD_INFO_COMMITS)$(BUILD_INFO_DIRTY)

ifeq ($(OS),Windows_NT)
  ifeq ($(shell uname -s),) # not in a bash-like shell
	export BUILD_DATE	 := "$(shell echo %date% %time%)"
	export BUILD_MACHINE := "$(shell echo %username%@%COMPUTERNAME%)"
  else # in a bash-like shell, like msys
	export BUILD_DATE	 := "$(shell date)"
	export BUILD_MACHINE := "$(shell echo `whoami`@`hostname`)"
  endif
else
	export BUILD_DATE	 := "$(shell date)"
	export BUILD_MACHINE := "$(shell echo `whoami`@`hostname`)"
endif


######################################
# Sources
######################################
# C sources
C_SOURCES =  $(wildcard libs/STM32CubeL0/Drivers/STM32L0xx_HAL_Driver/Src/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/system/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/radio/sx1276/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/peripherals/soft-se/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/mac/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/mac/region/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/boards/mcu/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/boards/B-L072Z-LRWAN1/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/apps/LoRaMac/common/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/apps/LoRaMac/common/LmHandler/*.c)
C_SOURCES += $(wildcard libs/LoRaMac-node/src/apps/LoRaMac/common/LmHandler/packages/*.c)
C_SOURCES += $(wildcard libs/nt3h2111-nfc-driver/*.c)
C_SOURCES += $(wildcard libs/nfc-utils/*.c)
C_SOURCES += $(wildcard libs/Si7210_driver/*.c)
C_SOURCES += $(wildcard libs/BME280_driver/*.c)
C_SOURCES += $(wildcard src/app/*.c)
C_SOURCES += $(wildcard src/board/*.c)
C_SOURCES += $(wildcard src/board/censis/*.c)
C_SOURCES += $(wildcard src/modules/*.c)
C_SOURCES += $(wildcard src/*.c)
C_SOURCES := $(filter-out libs/STM32CubeL0/Drivers/STM32L0xx_HAL_Driver/Src/stm32l0xx_hal_msp_template.c, $(C_SOURCES))

# ASM sources
ASM_SOURCES = src/startup_stm32l072xx.s

#######################################
# Binaries
#######################################
PREFIX = arm-none-eabi-
# The gcc compiler bin path can be either defined in make command via GCC_PATH variable (> make GCC_PATH=xxx)
# either it can be added to the PATH environment variable.
ifdef GCC_PATH
CC = $(GCC_PATH)/$(PREFIX)gcc
AS = $(GCC_PATH)/$(PREFIX)gcc -x assembler-with-cpp
CP = $(GCC_PATH)/$(PREFIX)objcopy
SZ = $(GCC_PATH)/$(PREFIX)size
else
CC = $(PREFIX)gcc
AS = $(PREFIX)gcc -x assembler-with-cpp
CP = $(PREFIX)objcopy
SZ = $(PREFIX)size
endif
HEX = $(CP) -O ihex
BIN = $(CP) -O binary -S
 
#######################################
# CFLAGS
#######################################
# cpu
CPU = -mcpu=cortex-m0plus

# mcu
MCU = $(CPU) -mthumb $(FPU) $(FLOAT-ABI)

# macros for gcc
# AS defines
AS_DEFS = 

# C Defines
C_DEFS  = -DUSE_HAL_DRIVER
C_DEFS += -DREGION_EU868
C_DEFS += -DUSE_B_L072Z_LRWAN1
C_DEFS += -DSTM32L072xx
C_DEFS += -DLOAD_LORA_KEYS 
C_DEFS += -DBME280_FLOAT_ENABLE
# Application Selection (Select only one)
C_DEFS += -DRUN_MAIN_APP
#C_DEFS += -DRUN_TEST_DOOR_APP
#C_DEFS += -DRUN_TEST_HALL_APP
# Version Information
C_DEFS += -DVERION_MAGIC_STRING=\"$(MAGIC_STRING)\" 
C_DEFS += -DVERSION_BUILD_DATE=\"$(BUILD_DATE)\"
C_DEFS += -DVERSION_BUILD_MACHINE=\"$(BUILD_MACHINE)\"
C_DEFS += -DVERSION_TAG=\"$(BUILD_TAG)\"
C_DEFS += -DVERSION_BUILD=\"$(BUILD_INFO)\"

# AS includes
AS_INCLUDES = 

# C includes 
C_INCLUDES =  -Ilibs/STM32CubeL0/Drivers/STM32L0xx_HAL_Driver/Inc
C_INCLUDES += -Ilibs/STM32CubeL0/Drivers/STM32L0xx_HAL_Driver/Inc/Legacy
C_INCLUDES += -Ilibs/STM32CubeL0/Drivers/CMSIS/Device/ST/STM32L0xx/Include
C_INCLUDES += -Ilibs/STM32CubeL0/Drivers/CMSIS/Include
C_INCLUDES += -Ilibs/LoRaMac-node/src/system
C_INCLUDES += -Ilibs/LoRaMac-node/src/radio
C_INCLUDES += -Ilibs/LoRaMac-node/src/radio/sx1276
#C_INCLUDES += -Ilibs/LoRaMac-node/src/peripherals
C_INCLUDES += -Ilibs/LoRaMac-node/src/peripherals/soft-se
C_INCLUDES += -Ilibs/LoRaMac-node/src/mac
C_INCLUDES += -Ilibs/LoRaMac-node/src/mac/region
C_INCLUDES += -Ilibs/LoRaMac-node/src/boards
#C_INCLUDES += -Ilibs/LoRaMac-node/src/boards/mcu/stm32
C_INCLUDES += -Ilibs/LoRaMac-node/src/boards/B-L072Z-LRWAN1
#C_INCLUDES += -Ilibs/LoRaMac-node/src/apps/LoRaMac/classA/B-L072Z-LRWAN1
C_INCLUDES += -Ilibs/LoRaMac-node/src/apps/LoRaMac/common
C_INCLUDES += -Ilibs/LoRaMac-node/src/apps/LoRaMac/common/LmHandler
C_INCLUDES += -Ilibs/LoRaMac-node/src/apps/LoRaMac/common/LmHandler/packages
#C_INCLUDES += -Ilibs/LoRaMac-node/src/boards/mcu/stm32/cmsis
C_INCLUDES += -Ilibs/nt3h2111-nfc-driver
C_INCLUDES += -Ilibs/nfc-utils
C_INCLUDES += -Ilibs/Si7210_driver
C_INCLUDES += -Ilibs/BME280_driver
C_INCLUDES += -Isrc/app
C_INCLUDES += -Isrc/board
C_INCLUDES += -Isrc/board/censis
C_INCLUDES += -Isrc/modules
C_INCLUDES += -Isrc

# compile gcc flags
ASFLAGS = $(MCU) $(AS_DEFS) $(AS_INCLUDES) $(OPT) -Wall -fdata-sections -ffunction-sections

CFLAGS = $(MCU) $(C_DEFS) $(C_INCLUDES) $(OPT) -Wall -fdata-sections -ffunction-sections

ifeq ($(DEBUG), 1)
CFLAGS += -g -gdwarf-2
endif

# Generate dependency information
CFLAGS += -MMD -MP -MF"$(@:%.o=%.d)"

#######################################
# LDFLAGS
#######################################
# link script
LDSCRIPT = src/STM32L072CZYx_FLASH.ld

# libraries
LIBS = -lc -lm -lnosys -u _printf_float
LIBDIR = 
LDFLAGS = $(MCU) -specs=nano.specs -T$(LDSCRIPT) $(LIBDIR) $(LIBS) -Wl,-Map=$(BUILD_DIR)/$(TARGET).map,--cref -Wl,--gc-sections

# default action: build all
all: $(BUILD_DIR)/$(TARGET).elf $(BUILD_DIR)/$(TARGET).hex $(BUILD_DIR)/$(TARGET).bin | version

#######################################
# build the application
#######################################
# list of objects
OBJECTS = $(addprefix $(BUILD_DIR)/,$(notdir $(C_SOURCES:.c=.o)))
vpath %.c $(sort $(dir $(C_SOURCES)))

# list of ASM program objects
OBJECTS += $(addprefix $(BUILD_DIR)/,$(notdir $(ASM_SOURCES:.s=.o)))
vpath %.s $(sort $(dir $(ASM_SOURCES)))

$(BUILD_DIR)/%.o: %.c Makefile | $(BUILD_DIR) 
	$(CC) -c $(CFLAGS) -Wa,-a,-ad,-alms=$(BUILD_DIR)/$(notdir $(<:.c=.lst)) $< -o $@

$(BUILD_DIR)/%.o: %.s Makefile | $(BUILD_DIR)
	$(AS) -c $(CFLAGS) $< -o $@

$(BUILD_DIR)/$(TARGET).elf: $(OBJECTS) Makefile
	$(CC) $(OBJECTS) $(LDFLAGS) -o $@
	$(SZ) $@

$(BUILD_DIR)/%.hex: $(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(HEX) $< $@
	
$(BUILD_DIR)/%.bin: $(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(BIN) $< $@	
	
$(BUILD_DIR):
	$(MKDIR) $@	

#######################################
# clean up
#######################################
clean:
	$(CLEANUP) $(BUILD_DIR)

#######################################
# Version file
#######################################
version:
	$(CLEANUP) VERSION.txt
	echo $(MAGIC_STRING) >> VERSION.txt
	echo $(BUILD_DATE) >> VERSION.txt
	echo $(BUILD_MACHINE) >> VERSION.txt
	echo $(BUILD_TAG) >> VERSION.txt
	echo $(BUILD_INFO) >> VERSION.txt

#######################################
# dependencies
#######################################
-include $(wildcard $(BUILD_DIR)/*.d)
