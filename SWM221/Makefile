CROSS_COMPILE ?= arm-none-eabi-
CC      = $(CROSS_COMPILE)gcc
OBJCOPY = $(CROSS_COMPILE)objcopy
OBJDUMP = $(CROSS_COMPILE)objdump


CFLAGS  = -mthumb -mcpu=cortex-m0 -mfloat-abi=soft -std=c99 -Wpointer-arith -DCHIP_SWM221
CFLAGS += -ffunction-sections -fdata-sections -fno-common -funsigned-char -mno-unaligned-access

DEBUG = 1
ifeq ($(DEBUG),1)
CFLAGS += -O0 -g
else
CFLAGS += -O2
endif

ASFLAGS = $(CFLAGS)

LINKER_SCRIPT = swm221.ld
LDFLAGS = -Wl,--gc-sections -Wl,-Map=$@.map -T $(LINKER_SCRIPT) -mthumb -mcpu=cortex-m0 -mfloat-abi=soft -specs=nosys.specs -specs=nano.specs -u _printf_float


INC =  -IAPP/
INC += -ICSL/CMSIS/CoreSupport/
INC += -ICSL/CMSIS/DeviceSupport/
INC += -ICSL/SWM221_StdPeriph_Driver/


APP_SRC = $(addprefix APP/,\
	main.c \
)

CSL_SRC = $(addprefix CSL/,\
	CMSIS/DeviceSupport/system_SWM221.c \
	SWM221_StdPeriph_Driver/SWM221_adc.c \
	SWM221_StdPeriph_Driver/SWM221_can.c \
	SWM221_StdPeriph_Driver/SWM221_crc.c \
	SWM221_StdPeriph_Driver/SWM221_div.c \
	SWM221_StdPeriph_Driver/SWM221_dma.c \
	SWM221_StdPeriph_Driver/SWM221_exti.c \
	SWM221_StdPeriph_Driver/SWM221_flash.c \
	SWM221_StdPeriph_Driver/SWM221_gpio.c \
	SWM221_StdPeriph_Driver/SWM221_i2c.c \
	SWM221_StdPeriph_Driver/SWM221_iofilt.c \
	SWM221_StdPeriph_Driver/SWM221_mpu.c \
	SWM221_StdPeriph_Driver/SWM221_port.c \
	SWM221_StdPeriph_Driver/SWM221_pwm.c \
	SWM221_StdPeriph_Driver/SWM221_qei.c \
	SWM221_StdPeriph_Driver/SWM221_qspi.c \
	SWM221_StdPeriph_Driver/SWM221_spi.c \
	SWM221_StdPeriph_Driver/SWM221_timr.c \
	SWM221_StdPeriph_Driver/SWM221_uart.c \
	SWM221_StdPeriph_Driver/SWM221_usart.c \
	SWM221_StdPeriph_Driver/SWM221_wdt.c \
)

CSL_SRS = $(addprefix CSL/,\
	CMSIS/DeviceSupport/startup/gcc/startup_SWM221.s \
)


BUILD ?= out

TARGET ?= SWM221_StdPeriph_Driver

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
