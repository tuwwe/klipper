# Main code build rules

src-y += sched.c command.c basecmd.c debugcmds.c
src-$(CONFIG_HAVE_GPIO) += gpiocmds.c stepper.c endstop.c
src-$(CONFIG_HAVE_GPIO_ADC) += adccmds.c
src-$(CONFIG_HAVE_GPIO_SPI) += spicmds.c thermocouple.c stepper_tmc51xx.c
src-$(CONFIG_HAVE_GPIO_HARD_PWM) += pwmcmds.c
