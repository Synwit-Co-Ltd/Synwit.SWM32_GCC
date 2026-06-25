CROSS_COMPILE ?= arm-none-eabi-
CC      = $(CROSS_COMPILE)gcc
OBJCOPY = $(CROSS_COMPILE)objcopy
OBJDUMP = $(CROSS_COMPILE)objdump


CFLAGS  = -mthumb -mcpu=cortex-m33 -mfloat-abi=soft -std=c99 -Wpointer-arith
CFLAGS += -ffunction-sections -fdata-sections -fno-common -funsigned-char -mno-unaligned-access

DEBUG = 1
ifeq ($(DEBUG),1)
CFLAGS += -O0 -g
else
CFLAGS += -O2
endif

ASFLAGS = $(CFLAGS)

LINKER_SCRIPT = swm330.ld
LDFLAGS = -Wl,--gc-sections -Wl,-Map=$@.map -T $(LINKER_SCRIPT) -mthumb -mcpu=cortex-m33 -mfloat-abi=soft -specs=nosys.specs -specs=nano.specs -u _printf_float


INC =  -IAPP/
INC += -ICSL/CMSIS/CoreSupport/
INC += -ICSL/CMSIS/DeviceSupport/
INC += -ICSL/SWM330_StdPeriph_Driver/


APP_SRC = $(addprefix APP/,\
	main.c \
)

CSL_SRC = $(addprefix CSL/,\
	CMSIS/DeviceSupport/system_SWM330.c \
	SWM330_StdPeriph_Driver/SWM330_adc.c \
	SWM330_StdPeriph_Driver/SWM330_can.c \
	SWM330_StdPeriph_Driver/SWM330_crc.c \
	SWM330_StdPeriph_Driver/SWM330_dac.c \
	SWM330_StdPeriph_Driver/SWM330_dma.c \
	SWM330_StdPeriph_Driver/SWM330_dma2d.c \
	SWM330_StdPeriph_Driver/SWM330_dvp.c \
	SWM330_StdPeriph_Driver/SWM330_exti.c \
	SWM330_StdPeriph_Driver/SWM330_flash.c \
	SWM330_StdPeriph_Driver/SWM330_gpio.c \
	SWM330_StdPeriph_Driver/SWM330_i2c.c \
	SWM330_StdPeriph_Driver/SWM330_i2s.c \
	SWM330_StdPeriph_Driver/SWM330_jpeg.c \
	SWM330_StdPeriph_Driver/SWM330_lcd.c \
	SWM330_StdPeriph_Driver/SWM330_port.c \
	SWM330_StdPeriph_Driver/SWM330_psram.c \
	SWM330_StdPeriph_Driver/SWM330_pwm.c \
	SWM330_StdPeriph_Driver/SWM330_qspi.c \
	SWM330_StdPeriph_Driver/SWM330_rdma.c \
	SWM330_StdPeriph_Driver/SWM330_rtc.c \
	SWM330_StdPeriph_Driver/SWM330_sdio.c \
	SWM330_StdPeriph_Driver/SWM330_sleep.c \
	SWM330_StdPeriph_Driver/SWM330_spi.c \
	SWM330_StdPeriph_Driver/SWM330_sra.c \
	SWM330_StdPeriph_Driver/SWM330_timr.c \
	SWM330_StdPeriph_Driver/SWM330_uart.c \
	SWM330_StdPeriph_Driver/SWM330_wdt.c \
)

CSL_SRS = $(addprefix CSL/,\
	CMSIS/DeviceSupport/startup/gcc/startup_SWM330.s \
)


BUILD ?= out

TARGET ?= SWM330_StdPeriph_Driver

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
