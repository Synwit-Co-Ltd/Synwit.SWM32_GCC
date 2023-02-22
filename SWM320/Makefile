CROSS_COMPILE ?= arm-none-eabi-
CC      = $(CROSS_COMPILE)gcc
OBJCOPY = $(CROSS_COMPILE)objcopy
OBJDUMP = $(CROSS_COMPILE)objdump


CFLAGS  = -mthumb -mcpu=cortex-m4 -mfloat-abi=soft -std=c99 -Wpointer-arith
CFLAGS += -ffunction-sections -fdata-sections -fno-common -funsigned-char -mno-unaligned-access

DEBUG = 1
ifeq ($(DEBUG),1)
CFLAGS += -O0 -g
else
CFLAGS += -O2
endif

ASFLAGS = $(CFLAGS)

LINKER_SCRIPT = swm320.ld
LDFLAGS = -Wl,--gc-sections -Wl,-Map=$@.map -T $(LINKER_SCRIPT) -mthumb -mcpu=cortex-m4 -mfloat-abi=soft -specs=nosys.specs -specs=nano.specs -u _printf_float


INC =  -IAPP/
INC += -ICSL/CMSIS/CoreSupport/
INC += -ICSL/CMSIS/DeviceSupport/
INC += -ICSL/SWM320_StdPeriph_Driver/


APP_SRC = $(addprefix APP/,\
	main.c \
)

CSL_SRC = $(addprefix CSL/,\
	CMSIS/DeviceSupport/system_SWM320.c \
	SWM320_StdPeriph_Driver/SWM320_adc.c \
	SWM320_StdPeriph_Driver/SWM320_can.c \
	SWM320_StdPeriph_Driver/SWM320_crc.c \
	SWM320_StdPeriph_Driver/SWM320_dma.c \
	SWM320_StdPeriph_Driver/SWM320_exti.c \
	SWM320_StdPeriph_Driver/SWM320_flash.c \
	SWM320_StdPeriph_Driver/SWM320_gpio.c \
	SWM320_StdPeriph_Driver/SWM320_i2c.c \
	SWM320_StdPeriph_Driver/SWM320_lcd.c \
	SWM320_StdPeriph_Driver/SWM320_norflash.c \
	SWM320_StdPeriph_Driver/SWM320_port.c \
	SWM320_StdPeriph_Driver/SWM320_pwm.c \
	SWM320_StdPeriph_Driver/SWM320_rtc.c \
	SWM320_StdPeriph_Driver/SWM320_sdio.c \
	SWM320_StdPeriph_Driver/SWM320_sdram.c \
	SWM320_StdPeriph_Driver/SWM320_spi.c \
	SWM320_StdPeriph_Driver/SWM320_sram.c \
	SWM320_StdPeriph_Driver/SWM320_timr.c \
	SWM320_StdPeriph_Driver/SWM320_uart.c \
	SWM320_StdPeriph_Driver/SWM320_wdt.c \
)

CSL_SRS = $(addprefix CSL/,\
	CMSIS/DeviceSupport/startup/gcc/startup_SWM320.s \
)


BUILD ?= out

TARGET ?= SWM320_StdPeriph_Driver

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
