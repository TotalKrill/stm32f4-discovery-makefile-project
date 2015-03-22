#Project main setup, theese things must be edited per cpu / programmer

PROJ_NAME=template

MCU=STM32F4xx

STARTUP_FILE=../Libraries/CMSIS/ST/STM32F4xx/Source/Templates/gcc_ride7/startup_stm32f4xx.s

LDSCRIPT = stm32_flash.ld

GDB_FLAGS = -ex "target remote | openocd -c \"gdb_port pipe; log_output openocd.log\" -f board/stm32f4discovery.cfg"

SEMIHOSTING_FLAGS = --specs=rdimon.specs -lc -lrdimon 

# Inputs: C Sources, Assembler Sources
SOURCES=main.c stm32f4xx_it.c system_stm32f4xx.c \
../Utilities/STM32F4-Discovery/stm32f4_discovery.c \

INCLUDES=

# Autosearch and include everything from the inc sublfolder and compile everything 
# from the src folder

PROJ = $(wildcard ./dev/*)
PROJ_INC += $(PROJ)/inc
PROJ_CSRCS += $(wildcard $(PROJ)/src/*.c)
PROJ_ASRCS += $(wildcard $(PROJ)/src/*.s)


# GNU ARM Embedded Toolchain
CC=arm-none-eabi-gcc
LD=arm-none-eabi-ld
AR=arm-none-eabi-ar
AS=arm-none-eabi-as
CP=arm-none-eabi-objcopy
OD=arm-none-eabi-objdump
GDB=arm-none-eabi-gdb

# Build Parameters: MCU Flags, Definitions, Includes, 
#                   Compile Flags, Linker Script, Linker Flags

CFLAGS=-fdiagnostics-color=always -g

MCFLAGS=-mcpu=cortex-m4 -mthumb -mlittle-endian \
-mfpu=fpv4-sp-d16 -mfloat-abi=hard -mthumb-interwork
DEFS=-DUSE_STDPERIPH_DRIVER -DSTM32F4XX


INCLUDES+=-I. \
-I../Utilities/STM32F4-Discovery \
-I../Libraries/CMSIS/Include \

STDLIB_INC+=../Libraries/$(MCU)_StdPeriph_Driver/inc \
	    ../Libraries/CMSIS/ST/$(MCU)/Include \

STDLIB_SRCS+=$(wildcard ../Libraries/$(MCU)_StdPeriph_Driver/src/*.c)


IINCDIR=$(patsubst %,-I%,$(STDLIB_INC) $(PROJ_INC))



CFLAGS+=-c $(MCFLAGS) $(DEFS) $(INCLUDES) $(IINCDIR)


LDFLAGS=-T $(LDSCRIPT) --specs=nosys.specs $(MCFLAGS)


# Compile everything form stdlibs
SOURCES+= $(STDLIB_SRCS)


ASMSOURCES= $(STARTUP_FILE)

# Outputs: Object Files, ELF Executable & 
#          Converted ELF Executable to Intel HEX format
OBJECTS=$(SOURCES:%.c=%.o)
OBJECTS+=$(ASMSOURCES:%.s=%.o)

EXECUTABLE=$(PROJ_NAME).elf
TARGET=$(PROJ_NAME).hex

# Build Rules
.PHONY: release
release: $(TARGET)

.PHONY: all
all: $(TARGET)
.PHONY: debug
debug: CFLAGS+=-g # Add debug flag
debug: LDFLAGS+=-g
debug: $(TARGET) 

$(TARGET): $(EXECUTABLE)
	$(CP) -O ihex $< $@
	@echo "Objcopy from ELF to IHEX complete!\n"

$(EXECUTABLE): $(OBJECTS)
	$(CC) $(LDFLAGS) $(OBJECTS) $(SEMIHOSTING_FLAGS) -o $@
	@echo "Linking complete!\n"

%.o: %.c
	$(CC) $(CFLAGS) $< -o $@
	@echo "Compiled "$<"!\n"

%.o: %.s
	$(CC) $(CFLAGS) $< -o $@
	@echo "Assambled "$<"!\n"

BOARDCONFIGPATH=board/stm32f4discovery.cfg	
.PHONY: clean
clean:
	rm -f $(OBJECTS) $(EXECUTABLE) $(TARGET)
gdb: $(EXECUTABLE)
	$(GDB) $< $(GDB_FLAGS)
test:
	@echo "$(EXECUTABLE)"
	
