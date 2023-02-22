CROSS_COMPILE ?= arm-none-eabi-
CC      = $(CROSS_COMPILE)gcc
OBJCOPY = $(CROSS_COMPILE)objcopy
OBJDUMP = $(CROSS_COMPILE)objdump


CFLAGS  = -mthumb -mcpu=cortex-m0 -mfloat-abi=soft -std=c99 -Wpointer-arith
CFLAGS += -ffunction-sections -fdata-sections -fno-common -funsigned-char -mno-unaligned-access

DEBUG = 1
ifeq ($(DEBUG),1)
CFLAGS += -O0 -g
else
CFLAGS += -O2
endif

ASFLAGS = $(CFLAGS)

LINKER_SCRIPT = swm190.ld
LDFLAGS = -Wl,--gc-sections -Wl,-Map=$@.map -T $(LINKER_SCRIPT) -mthumb -mcpu=cortex-m0 -mfloat-abi=soft -specs=nosys.specs -specs=nano.specs -u _printf_float


INC =  -IAPP/
INC += -ICSL/CMSIS/CoreSupport/
INC += -ICSL/CMSIS/DeviceSupport/
INC += -ICSL/SWM190_StdPeriph_Driver/


APP_SRC = $(addprefix APP/,\
	main.c \
)

CSL_SRC = $(addprefix CSL/,\
	CMSIS/DeviceSupport/system_SWM190.c \
	SWM190_StdPeriph_Driver/SWM190_adc.c \
	SWM190_StdPeriph_Driver/SWM190_cache.c \
	SWM190_StdPeriph_Driver/SWM190_cordic.c \
	SWM190_StdPeriph_Driver/SWM190_div.c \
	SWM190_StdPeriph_Driver/SWM190_dma.c \
	SWM190_StdPeriph_Driver/SWM190_exti.c \
	SWM190_StdPeriph_Driver/SWM190_flash.c \
	SWM190_StdPeriph_Driver/SWM190_gpio.c \
	SWM190_StdPeriph_Driver/SWM190_i2c.c \
	SWM190_StdPeriph_Driver/SWM190_port.c \
	SWM190_StdPeriph_Driver/SWM190_pwm.c \
	SWM190_StdPeriph_Driver/SWM190_rtc.c \
	SWM190_StdPeriph_Driver/SWM190_sleep.c \
	SWM190_StdPeriph_Driver/SWM190_spi.c \
	SWM190_StdPeriph_Driver/SWM190_timr.c \
	SWM190_StdPeriph_Driver/SWM190_uart.c \
	SWM190_StdPeriph_Driver/SWM190_wdt.c \
)

CSL_SRS = $(addprefix CSL/,\
	CMSIS/DeviceSupport/startup/gcc/startup_SWM190.s \
)


BUILD ?= out

TARGET ?= SWM190_StdPeriph_Driver

OBJS = $(addprefix $(BUILD)/, $(APP_SRC:.c=.o) $(CSL_SRC:.c=.o) $(CSL_SRS:.s=.o))


all: $(BUILD)/$(TARGET).elf

$(BUILD)/$(TARGET).elf: $(OBJS)
	$(CC) -o $@ $(LDFLAGS) $(OBJS)
	$(OBJCOPY) -O ihex   $@ $(BUILD)/$(TARGET).hex
	$(OBJCOPY) -O binary $@ $(BUILD)/$(TARGET).bin
	$(OBJDUMP) -d $@ > $(BUILD)/$(TARGET).elf.dis

$(BUILD)/%.o: %.c
	echo "Compiling $^"
	$(CC) -c -o $@ $(CFLAGS) $(INC) $<

$(BUILD)/%.o: %.s
	echo "Assembling $^"
	$(CC) -c -o $@ $(ASFLAGS) $<


# $(sort $(var)) removes duplicates
#
# The net effect of this, is it causes the objects to depend on the
# object directories (but only for existence), and the object directories
# will be created if they don't exist.
OBJ_DIRS = $(sort $(dir $(OBJS)))
$(OBJS): | $(OBJ_DIRS)
$(OBJ_DIRS):
	mkdir -p $@


clean:
	rm -rf $(BUILD)/*
