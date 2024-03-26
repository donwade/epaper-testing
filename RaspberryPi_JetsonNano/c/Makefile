DIR_Config	 = ./lib/Config
DIR_EPD		 = ./lib/e-Paper
DIR_FONTS	 = ./lib/Fonts
DIR_GUI		 = ./lib/GUI
DIR_Examples = ./examples
DIR_BIN		 = ./bin

DIR_INC_WX = ./include.wx
DIR_SRC_WX = ./src.wx
DIR_LIB_WX = ./lib.wx


EPD = NULL
ifeq ($(EPD), epd7in5)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5_test.c
else ifeq ($(EPD), epd7in5V2)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5_V2.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5_V2_test.c
else ifeq ($(EPD), epd7in5V2_old)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5_V2_old.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5_V2_test_old.c
else ifeq ($(EPD), epd7in5bc)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5bc.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5bc_test.c
else ifeq ($(EPD), epd7in5bV2)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5b_V2.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5b_V2_test.c
else ifeq ($(EPD), epd7in5HD)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5_HD.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5_HD_test.c
else ifeq ($(EPD), epd7in5bHD)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5b_HD.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5b_HD_test.c
else 
    OBJ_C_EPD = NULL
    OBJ_C_Examples = NULL
endif

C_SRC_OLD = $(wildcard ${OBJ_C_EPD} ${DIR_GUI}/*.c ${OBJ_C_Examples} ${DIR_Examples}/main.c ${DIR_Examples}/ImageData2.c ${DIR_Examples}/ImageData.c ${DIR_FONTS}/*.c )
$(info $$C_SCR_OLD = $(C_SRC_OLD))

CPP_SRC_NEW = $(wildcard ${DIR_SRC_WX}/*.cpp )
$(info $$CPP_SRC_NEW = $(CPP_SRC_NEW))

#OBJ_C = ${CPP_SRC_NEW} ${C_SRC_OLD}

C_OBJECT_LIST = $(patsubst %.c,${DIR_BIN}/%.o,$(notdir ${C_SRC_OLD}))
CPP_OBJECT_LIST = $(patsubst %.cpp,${DIR_BIN}/%.o,$(notdir ${CPP_SRC_NEW}))

FULL_OBJECT_LIST = ${C_OBJECT_LIST} ${CPP_OBJECT_LIST}
$(info $$FULL_OBJECT_LIST = $(FULL_OBJECT_LIST))

RPI_HW_OBJ = $(wildcard $(DIR_BIN)/dev_hardware_SPI.o $(DIR_BIN)/RPI_gpiod.o $(DIR_BIN)/DEV_Config.o )
JETSON_DEV_C = $(wildcard $(DIR_BIN)/sysfs_software_spi.o $(DIR_BIN)/sysfs_gpio.o $(DIR_BIN)/DEV_Config.o )


DEBUG = -D DEBUG

# USELIB_RPI = USE_BCM2835_LIB
# USELIB_RPI = USE_WIRINGPI_LIB
USELIB_RPI = USE_LGPIO_LIB
# USELIB_RPI = USE_DEV_LIB

LIB_RPI=-Wl,--gc-sections
ifeq ($(USELIB_RPI), USE_BCM2835_LIB)
	LIB_RPI += -lbcm2835 -lm 
else ifeq ($(USELIB_RPI), USE_WIRINGPI_LIB)
	LIB_RPI += -lwiringPi -lm 
else ifeq ($(USELIB_RPI), USE_LGPIO_LIB)
	LIB_RPI += -llgpio -lm 
else ifeq ($(USELIB_RPI), USE_DEV_LIB)
	LIB_RPI += -lgpiod -lm 
endif
DEBUG_RPI = -D $(USELIB_RPI) -D RPI

USELIB_JETSONI = USE_DEV_LIB
# USELIB_JETSONI = USE_HARDWARE_LIB
ifeq ($(USELIB_JETSONI), USE_DEV_LIB)
	LIB_JETSONI = -lm 
else ifeq ($(USELIB_JETSONI), USE_HARDWARE_LIB)
	LIB_JETSONI = -lm 
endif
DEBUG_JETSONI = -D $(USELIB_JETSONI) -D JETSON

.PHONY : RPI JETSON clean

RPI:RPI_HW RPI_epd 

TARGET = epd
CC = g++
MSG = -g -O -ffunction-sections -fdata-sections -Wall -fpermissive
CFLAGS += $(MSG) -D $(EPD)
CXXFLAGS += ${CFLAGS}

RPI_epd:${FULL_OBJECT_LIST}
	echo $(@)
	$(CC) $(CFLAGS) -D RPI $(FULL_OBJECT_LIST) $(RPI_HW_OBJ) -o $(TARGET) $(LIB_RPI) $(DEBUG)
	echo 

$(shell mkdir -p $(DIR_BIN))

${DIR_BIN}/%.o:$(DIR_Examples)/%.c
	$(CC) $(CFLAGS) -c	$< -o $@ -I $(DIR_Config) -I $(DIR_GUI) -I $(DIR_EPD) $(DEBUG)
	echo "***** rule $(@) done -------------------" 
	
${DIR_BIN}/%.o:$(DIR_EPD)/%.c
	$(CC) $(CFLAGS) -c	$< -o $@ -I $(DIR_Config) $(DEBUG)
	echo "***** rule $(@) done -------------------" 

${DIR_BIN}/%.o:$(DIR_FONTS)/%.c 
	$(CC) $(CFLAGS) -c	$< -o $@ $(DEBUG)
	echo "***** rule $(@) done -------------------" 

${DIR_BIN}/%.o:$(DIR_GUI)/%.c
	$(CC) $(CFLAGS) -c	$< -o $@ -I $(DIR_Config) $(DEBUG)
	echo "***** rule $(@) done -------------------" 

${DIR_BIN}/%.o:$(DIR_SRC_WX)/%.cpp
	$(CC) $(CFLAGS) -c	$< -o $@ -I $(DIR_Config) $(DEBUG)
	echo "***** rule $(@) done -------------------" 

RPI_HW:
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/dev_hardware_SPI.c -o $(DIR_BIN)/dev_hardware_SPI.o $(LIB_RPI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/RPI_gpiod.c -o $(DIR_BIN)/RPI_gpiod.o $(LIB_RPI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/DEV_Config.c -o $(DIR_BIN)/DEV_Config.o $(LIB_RPI) $(DEBUG)
	echo "***** rule $(@) done -------------------" 
 	
JETSON_DEV:
	$(CC) $(CFLAGS) $(DEBUG_JETSONI) -c	 $(DIR_Config)/sysfs_software_spi.c -o $(DIR_BIN)/sysfs_software_spi.o $(LIB_JETSONI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_JETSONI) -c	 $(DIR_Config)/sysfs_gpio.c -o $(DIR_BIN)/sysfs_gpio.o $(LIB_JETSONI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_JETSONI) -c	 $(DIR_Config)/DEV_Config.c -o $(DIR_BIN)/DEV_Config.o $(LIB_JETSONI)  $(DEBUG)

clean :
	rm $(DIR_BIN)/*.* 
	rm $(TARGET) 

