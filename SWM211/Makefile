CROSS_COMPILE ?= arm-none-eabi-
CC      = $(CROSS_COMPILE)gcc
OBJCOPY = $(CROSS_COMPILE)objcopy
OBJDUMP = $(CROSS_COMPILE)objdump


CFLAGS  = -mthumb -mcpu=cortex-m0 -mfloat-abi=soft -std=c99 -Wpointer-arith -DCHIP_SWM211
CFLAGS += -ffunction-sections -fdata-sections -fno-common -funsigned-char -mno-unaligned-access

DEBUG = 1
ifeq ($(DEBUG),1)
CFLAGS += -O0 -g
else
CFLAGS += -O2
endif

ASFLAGS = $(CFLAGS)

LINKER_SCRIPT = swm211.ld
LDFLAGS = -Wl,--gc-sections -Wl,-Map=$@.map -T $(LINKER_SCRIPT) -mthumb -mcpu=cortex-m0 -mfloat-abi=soft -specs=nosys.specs -specs=nano.specs -u _printf_float


INC =  -IAPP/
INC += -ICSL/CMSIS/CoreSupport/
INC += -ICSL/CMSIS/DeviceSupport/
INC += -ICSL/SWM2X1_StdPeriph_Driver/


APP_SRC = $(addprefix APP/,\
	main.c \
)

CSL_SRC = $(addprefix CSL/,\
	CMSIS/DeviceSupport/system_SWM211.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_adc.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_can.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_cordic.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_div.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_dma.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_exti.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_gpio.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_i2c.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_iofilt.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_mpu.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_port.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_pwm.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_qei.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_rtc.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_sleep.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_spi.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_timr.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_uart.c \
	SWM2X1_StdPeriph_Driver/SWM2X1_wdt.c \
	SWM2X1_StdPeriph_Driver/SWM211_flash.c \
)

CSL_SRS = $(addprefix CSL/,\
	CMSIS/DeviceSupport/startup/gcc/startup_SWM211.s \
)


BUILD ?= out

TARGET ?= SWM211_StdPeriph_Driver

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
