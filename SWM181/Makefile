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

LINKER_SCRIPT = swm181.ld
LDFLAGS = -Wl,--gc-sections -Wl,-Map=$@.map -T $(LINKER_SCRIPT) -mthumb -mcpu=cortex-m0 -mfloat-abi=soft -specs=nosys.specs -specs=nano.specs -u _printf_float


INC =  -IAPP/
INC += -ICSL/CMSIS/CoreSupport/
INC += -ICSL/CMSIS/DeviceSupport/
INC += -ICSL/SWM181_StdPeriph_Driver/


APP_SRC = $(addprefix APP/,\
	main.c \
)

CSL_SRC = $(addprefix CSL/,\
	CMSIS/DeviceSupport/system_SWM181.c \
	SWM181_StdPeriph_Driver/SWM181_adc.c \
	SWM181_StdPeriph_Driver/SWM181_cache.c \
	SWM181_StdPeriph_Driver/SWM181_can.c \
	SWM181_StdPeriph_Driver/SWM181_cmp.c \
	SWM181_StdPeriph_Driver/SWM181_cordic.c \
	SWM181_StdPeriph_Driver/SWM181_div.c \
	SWM181_StdPeriph_Driver/SWM181_dma.c \
	SWM181_StdPeriph_Driver/SWM181_exti.c \
	SWM181_StdPeriph_Driver/SWM181_flash.c \
	SWM181_StdPeriph_Driver/SWM181_gpio.c \
	SWM181_StdPeriph_Driver/SWM181_i2c.c \
	SWM181_StdPeriph_Driver/SWM181_irqmux.c \
	SWM181_StdPeriph_Driver/SWM181_port.c \
	SWM181_StdPeriph_Driver/SWM181_pwm.c \
	SWM181_StdPeriph_Driver/SWM181_sdadc.c \
	SWM181_StdPeriph_Driver/SWM181_slcd.c \
	SWM181_StdPeriph_Driver/SWM181_sleep.c \
	SWM181_StdPeriph_Driver/SWM181_spi.c \
	SWM181_StdPeriph_Driver/SWM181_timr.c \
	SWM181_StdPeriph_Driver/SWM181_uart.c \
	SWM181_StdPeriph_Driver/SWM181_wdt.c \
)

CSL_SRS = $(addprefix CSL/,\
	CMSIS/DeviceSupport/startup/gcc/startup_SWM181.s \
)


BUILD ?= out

TARGET ?= SWM181_StdPeriph_Driver

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
