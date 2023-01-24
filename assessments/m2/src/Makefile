#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# <Put a Description Here>
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <Put a description of the supported targets here>
#
# Platform Overrides:
#      <Put a description of the supported Overrides here
#
#------------------------------------------------------------------------------
include sources.mk

TARGET = c1m2
GFLAGS = \
	   -Wall \
	   -Werror \
	   -g \
	   -O0 \
           -std = c99 

# Platform Overrides
ifeq ($(PLATFORM),HOST)
	CC = gcc
	LD = ld
	CPPFLAGS = -DHOST $(INCLUDES)
	SIZE = size
	LDFLAGS = -Wl,-Map=$(TARGET).map
	OBJDUMP = objdump

else ifeq ($(PLATFORM),MSP432)
	CC = arm-none-eabi-gcc
        LD = arm-none-eabi-ld
	LINKER_FILE = ../msp432p401r.lds
	LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
	CPU = cortex-m4
	ARCH = armv7e-m
	SPECS = nosys.specs
	SOURCES+= 	./startup_msp432p401r_gcc.c \
				./system_msp432p401r.c \
				./interrupts_msp432p401r_gcc.c
	INCLUDES+= 	-I ../include/CMSIS/ \
				-I ../include/msp432/
	CFLAGS = 	$(GFLAGS) \
				-mcpu=$(CPU) \
				--specs=$(SPECS) \
				-mthumb \
				-march=$(ARCH) \
				-mfloat-abi=hard \
				-mfpu=fpv4-sp-d16\
	CPPFLAGS = -DMSP432 $(INCLUDES)
	OBJDUMP = arm-none-eabi-objdump

		#Size Utility
	SIZE = arm-none-eabi-size 

endif

PREP = $(SOURCES:.c=.i)	# Preprocessor Files
DEPS = $(SOURCES:.c=.d)	# Dependancy Files
ASMS = $(SOURCES:.c=.asm) # Assembly Files
OBJS = $(SOURCES:.c=.o)	# Object files

%.i: %.c
	$(CC) -E $< $(CFLAGS) $(CPPFLAGS) -o $@
%.asm: %.c
	$(CC) -S $< $(CFLAGS) $(CPPFLAGS) -o $@

%.o: %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -o $@
%.d: %c
	$(CC) -E -M $<  $(CPPFLAGS) -o $@

.PHONY: build
build: $(TARGET).out
$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@
	$(SIZE) $@
.PHONY: compile-all
compile-all: $(OBJS)
	$(CC) $(CPPFLAGS) $(OBJS) -c $(CFLAGS) -o $(TARGET).o

.PHONY: clean
clean: 
	rm -f $(TARGET).out $(TARGET).asm $(TARGET).map  $(PREP) $(ASMS) $(DEPS) $(OBJS)


