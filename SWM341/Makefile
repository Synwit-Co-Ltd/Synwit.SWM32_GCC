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

LINKER_SCRIPT = swm341.ld
LDFLAGS = -Wl,--gc-sections -Wl,-Map=$@.map -T $(LINKER_SCRIPT) -mthumb -mcpu=cortex-m33 -mfloat-abi=soft -specs=nosys.specs -specs=nano.specs -u _printf_float


INC =  -IAPP/
INC += -ICSL/CMSIS/CoreSupport/
INC += -ICSL/CMSIS/DeviceSupport/
INC += -ICSL/SWM341_StdPeriph_Driver/


APP_SRC = $(addprefix APP/,\
	main.c \
)

CSL_SRC = $(addprefix CSL/,\
	CMSIS/DeviceSupport/system_SWM341.c \
	SWM341_StdPeriph_Driver/SWM341_adc.c \
	SWM341_StdPeriph_Driver/SWM341_can.c \
	SWM341_StdPeriph_Driver/SWM341_cordic.c \
	SWM341_StdPeriph_Driver/SWM341_crc.c \
	SWM341_StdPeriph_Driver/SWM341_dac.c \
	SWM341_StdPeriph_Driver/SWM341_div.c \
	SWM341_StdPeriph_Driver/SWM341_dma.c \
	SWM341_StdPeriph_Driver/SWM341_dma2d.c \
	SWM341_StdPeriph_Driver/SWM341_exti.c \
	SWM341_StdPeriph_Driver/SWM341_flash.c \
	SWM341_StdPeriph_Driver/SWM341_gpio.c \
	SWM341_StdPeriph_Driver/SWM341_i2c.c \
	SWM341_StdPeriph_Driver/SWM341_jpeg.c \
	SWM341_StdPeriph_Driver/SWM341_lcd.c \
	SWM341_StdPeriph_Driver/SWM341_port.c \
	SWM341_StdPeriph_Driver/SWM341_pwm.c \
	SWM341_StdPeriph_Driver/SWM341_rtc.c \
	SWM341_StdPeriph_Driver/SWM341_sdio.c \
	SWM341_StdPeriph_Driver/SWM341_sdram.c \
	SWM341_StdPeriph_Driver/SWM341_sfc.c \
	SWM341_StdPeriph_Driver/SWM341_sleep.c \
	SWM341_StdPeriph_Driver/SWM341_spi.c \
	SWM341_StdPeriph_Driver/SWM341_timr.c \
	SWM341_StdPeriph_Driver/SWM341_uart.c \
	SWM341_StdPeriph_Driver/SWM341_usbd.c \
	SWM341_StdPeriph_Driver/SWM341_usbh.c \
	SWM341_StdPeriph_Driver/SWM341_wdt.c \
)

CSL_SRS = $(addprefix CSL/,\
	CMSIS/DeviceSupport/startup/gcc/startup_SWM341.s \
)


BUILD ?= out

TARGET ?= SWM341_StdPeriph_Driver

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
