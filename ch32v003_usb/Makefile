######################################
# target
######################################
TARGET = ch32v003_usb
CH32V003FUN?=../../ch32v003fun

######################################
# building variables
######################################
# debug build?
DEBUG = 1
# optimization
OPT = -Og


#######################################
# paths
#######################################
# Build path
BUILD_DIR = build

######################################
# source
######################################
# C sources
C_SOURCES = $(wildcard src/*.c)

# ASM sources
ASM_SOURCES = $(wildcard src/*.S)
# startup_stm32f411xe.s


#######################################
# binaries
#######################################
PREFIX = riscv64-unknown-elf-
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
CFLAGS+= \
	-Os -flto -ffunction-sections \
	-static-libgcc \
	-march=rv32ec \
	-mabi=ilp32e \
	-I/usr/include/newlib \
	-Iinclude \
	-nostdlib \
	-I. -Wall

ifeq ($(DEBUG), 1)
CFLAGS += -g -gdwarf-2
endif


# Generate dependency information
CFLAGS += -MMD -MP -MF"$(@:%.o=%.d)"


#######################################
# LDFLAGS
#######################################
# link script
LINKER_SCRIPT = ch32v003fun.ld

# libraries
LDFLAGS+=-T $(LINKER_SCRIPT) -Wl,--gc-sections -Lmisc -lgcc

vpath %.c $(sort $(dir $(C_SOURCES)))

# default action: build all
all: $(BUILD_DIR)/$(TARGET).elf $(BUILD_DIR)/$(TARGET).hex $(BUILD_DIR)/$(TARGET).bin | $(BUILD_DIR)


#######################################
# build the application
#######################################
# list of objects
$(BUILD_DIR)/$(TARGET).elf : $(C_SOURCES) $(ASM_SOURCES) | $(BUILD_DIR)
	$(CC) -o $@ $^ $(CFLAGS) $(LDFLAGS)

$(BUILD_DIR)/$(TARGET).bin : $(BUILD_DIR)/$(TARGET).elf | $(BUILD_DIR)
	$(SZ) $^
	$(PREFIX)objdump -S $^ > $(BUILD_DIR)/$(TARGET).lst
	$(PREFIX)objdump -t $^ > $(BUILD_DIR)/$(TARGET).map
	$(CP) -O binary $< $(BUILD_DIR)/$(TARGET).bin
	$(CP) -O ihex $< $(BUILD_DIR)/$(TARGET).hex

$(BUILD_DIR)/%.hex: $(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(HEX) $< $@

$(BUILD_DIR):
	mkdir build

#######################################
# clean up
#######################################
clean:
	-rm -fR $(BUILD_DIR)
  
#######################################
# dependencies
#######################################
-include $(wildcard $(BUILD_DIR)/*.d)

# *** EOF ***
