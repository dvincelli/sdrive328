###############################################################################
# Makefile for the project SDrive
###############################################################################

## General Flags
PROJECT = SDrive
#MCU = atmega8
MCU = atmega328p
TARGET = SDrive.elf
CC = avr-gcc

## Options common to compile, link and assembly rules
COMMON = -mmcu=$(MCU)

## Compile options common for all C compilation units.
CFLAGS = $(COMMON)
#CFLAGS += -Wall -w -Wtraditional -Wshadow -pedantic -Wa,-gdwarf2     -DF_CPU=14318180UL -Os -funsigned-char -fpack-struct
CFLAGS += -Wall -w -Wtraditional -Wshadow -pedantic -Wa,-gdwarf2     -DF_CPU=16000000UL -Os -funsigned-char -fpack-struct
CFLAGS += -MD -MP -MT $(*F).o -MF dep/$(@F).d 

## Assembly specific flags
ASMFLAGS = $(COMMON)
ASMFLAGS += $(CFLAGS)
ASMFLAGS += -x assembler-with-cpp -Wa,-gdwarf2

## Linker flags
LDFLAGS = $(COMMON)
LDFLAGS +=  -Wl,-Map=SDrive.map


## Intel Hex file production flags
HEX_FLASH_FLAGS = -R .eeprom

HEX_EEPROM_FLAGS = -j .eeprom
HEX_EEPROM_FLAGS += --set-section-flags=.eeprom="alloc,load"
HEX_EEPROM_FLAGS += --change-section-lma .eeprom=0 --no-change-warnings


## Include Directories
INCLUDES = -I"/usr/lib/avr/include" -I"/usr/lib/avr/bin" -I"usr/lib/avr/lib"

## Objects that must be built in order to link
OBJECTS = SDrive.o spi.o mmc.o fat.o # delay100us.o 

## Objects explicitly added by the user
LINKONLYOBJECTS = 

## Build
all: $(TARGET) SDrive.hex SDrive.eep SDrive.lss size

## Compile
delay100us.o: ../delay100us.s
	$(CC) $(INCLUDES) $(ASMFLAGS) -c  $<

SDrive.o: ../SDrive.c
	$(CC) $(INCLUDES) $(CFLAGS) -c  $<

spi.o: ../spi.c
	$(CC) $(INCLUDES) $(CFLAGS) -c  $<

mmc.o: ../mmc.c
	$(CC) $(INCLUDES) $(CFLAGS) -c  $<

fat.o: ../fat.c
	$(CC) $(INCLUDES) $(CFLAGS) -c  $<

##Link
$(TARGET): $(OBJECTS)
	 $(CC) $(LDFLAGS) $(OBJECTS) $(LINKONLYOBJECTS) $(LIBDIRS) $(LIBS) -o $(TARGET)

%.hex: $(TARGET)
	avr-objcopy -O ihex $(HEX_FLASH_FLAGS)  $< $@

%.eep: $(TARGET)
	-avr-objcopy $(HEX_EEPROM_FLAGS) -O ihex $< $@ || exit 0

%.lss: $(TARGET)
	avr-objdump -h -S $< > $@

size: ${TARGET}
	@echo
	@avr-size -C --mcu=${MCU} ${TARGET}

## Clean target
.PHONY: clean
clean:
	-rm -rf $(OBJECTS) SDrive.elf dep/* SDrive.hex SDrive.eep SDrive.lss SDrive.map

## Upload to device
.PHONY: flash
flash:
	avrdude -v -v -v -v -e -p atmega328p -cusbasp -Pusb -Uflash:w:SDrive.hex:i

.PHONY: eeprom
eeprom:
	avrdude -v -v -v -v -p atmega328p -cusbasp -Pusb -Ueeprom:w:SDrive.eep:i 

.PHONY: fuse
fuse:
	avrdude -v -v -v -v -e -p atmega328p -cusbasp -Pusb -Ulfuse:w:0xff:m -Uhfuse:w:0xde:m -Uefuse:w:0x05:m

.PHONY: upload
upload: flash fuse eeprom

## Other dependencies
-include $(shell mkdir dep 2>/dev/null) $(wildcard dep/*)

