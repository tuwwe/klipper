# lpc176x build rules

# Setup the toolchain
CROSS_PREFIX=arm-none-eabi-

CFLAGS += -mthumb -mcpu=cortex-m3 -O2
CFLAGS += -flto -fwhole-program -fno-use-linker-plugin
CFLAGS += -fno-exceptions -fno-delete-null-pointer-checks
CFLAGS += -Ilib/lpc176x/device -Ilib/lpc176x/device -Ilib/lpc176x/cmsis

CFLAGS_klipper.elf += -T $(OUT)LPC1768.ld
CFLAGS_klipper.elf += --specs=nano.specs --specs=nosys.specs

# Add source files
src-y += lpc176x/main.c lpc176x/timer.c lpc176x/gpio.c lpc176x/i2c.c
src-y += generic/crc16_ccitt.c generic/alloc.c
src-y += generic/armcm_irq.c generic/timer_irq.c
src-y += ../lib/lpc176x/device/system_LPC17xx.c
src-$(CONFIG_USBSERIAL) += lpc176x/usbserial.c generic/usb_cdc.c
src-$(CONFIG_SERIAL) += lpc176x/serial.c generic/serial_irq.c

# Add the TOOLCHAIN_GCC_ARM files to the build
asm-y := ../lib/lpc176x/device/TOOLCHAIN_GCC_ARM/startup_LPC17xx.S

target-y := $(OUT)LPC1768.ld $(target-y)

$(OUT)LPC1768.ld: lib/lpc176x/device/TOOLCHAIN_GCC_ARM/LPC1768.ld  $(OUT)autoconf.h $(OUT)board-link
	@echo "  Preprocessing $@"
	$(Q)$(CPP) -P -MD -MT $@ -DMBED_APP_START=$(CONFIG_FLASH_START) -DMBED_APP_SIZE="(512K - $(CONFIG_FLASH_START))" $< -o $@

# Build the additional bin output file
target-y += $(OUT)klipper.bin
target-y += $(OUT)klipper.hex

$(OUT)klipper.bin: $(OUT)klipper.elf
	@echo "  Creating bin file $@"
	$(Q)$(OBJCOPY) -O binary $< $@

$(OUT)klipper.hex: $(OUT)klipper.elf
	@echo "  Creating hex file $@"
	$(Q)$(OBJCOPY) -R .stack -O ihex $< $@

$(OUT)klipper.disasm: $(OUT)klipper.elf
	@echo "  Creating disasm file $@"
	$(Q)$(OBJDUMP)  -d -f -M reg-names-std --demangle $< > $@

ifeq ($(CONFIG_SMOOTHIEWARE_BOOTLOADER),y)
flash: $(OUT)klipper.bin
	@echo ""
	@echo "  To flash a Smoothieboard, copy the $(OUT)klipper.bin to a file"
	@echo "  named firmware.bin on an SD card and then restart the"
	@echo "  Smoothieboard with that SD card."
else
LISP := $(shell command -v lpc21isp 2> /dev/null)
flash: $(OUT)klipper.hex
	@echo ""
ifeq ($(LSIP),)
	@echo "  lpc2lisp is not installed. Please install it and try again."
else
	@echo "  Flashing $^ to $(FLASH_DEVICE) via lpc21isp"
	$(Q)if [ -z $(FLASH_DEVICE) ]; then echo "Please specify FLASH_DEVICE"; exit 1; fi
	$(Q)lpc21isp $(OUT)klipper.hex $(FLASH_DEVICE) 57600 12000
endif
endif
