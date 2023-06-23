cmake_minimum_required(VERSION 3.15.3)

project("STM32-F446RE-ADC-UART-DataTransfer")

set(CMAKE_C_COMPILER_WORKS 1)

#Taken from https://dev.to/younup/cmake-on-stm32-the-beginning-3766

#compile with command: 
#cmake -G "MinGW Makefiles" -DCMAKE_TOOLCHAIN_FILE=../arm-none-eabi-gcc.cmake -DCMAKE_BUILD_TYPE=Debug ..

# Enable C and ASM and require to use C99 standard
enable_language(C ASM)
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)


file(GLOB_RECURSE HAL_DRIVER_FILES
  "${CMAKE_SOURCE_DIR}/Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal*.c"
)

#Create a variable with the list of generated source files
set(STM32CUBEMX_GENERATED_FILES
		
		${HAL_DRIVER_FILES}
		Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_ll_adc.c
		
        Core/Inc/main.h
        Core/Inc/stm32f4xx_hal_conf.h
        Core/Inc/stm32f4xx_it.h

        Core/Src/main.c
        Core/Src/stm32f4xx_hal_msp.c
        Core/Src/stm32f4xx_it.c
        Core/Src/system_stm32f4xx.c

        startup_stm32f446xx.s
)

# add_executable() to create an executable with this list
set(EXECUTABLE ${PROJECT_NAME}.out)

add_executable(${EXECUTABLE} ${STM32CUBEMX_GENERATED_FILES})

#to add macro definitions
target_compile_definitions(${EXECUTABLE} PRIVATE
        -DUSE_HAL_DRIVER
        -DSTM32F446xx
        )

#to configure the compiler's include path
target_include_directories(${EXECUTABLE} PRIVATE
        Core/Inc
        Drivers/STM32F4xx_HAL_Driver/Inc
        Drivers/CMSIS/Device/ST/STM32F4xx/Include
        Drivers/CMSIS/Include
        )

# to set the compiler's options
target_compile_options(${EXECUTABLE} PRIVATE
        -mcpu=cortex-m4
        -mthumb
        -mfpu=fpv4-sp-d16
        -mfloat-abi=hard

        -fdata-sections
        -ffunction-sections

        -Wall

        $<$<CONFIG:Debug>:-Og>
        )

#to set the linker's options
target_link_options(${EXECUTABLE} PRIVATE
        -T${CMAKE_SOURCE_DIR}/STM32F446RETX_FLASH.ld
        -mcpu=cortex-m4
        -mthumb
        -mfpu=fpv4-sp-d16
        -mfloat-abi=hard
        -specs=nano.specs
        -specs=nosys.specs
        -lc
        -lm
        -lnosys
        -Wl,-Map=${PROJECT_NAME}.map,--cref
        -Wl,--gc-sections
        )

# Print executable size
add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-size ${EXECUTABLE})

# Create hex file
add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-objcopy -O ihex ${EXECUTABLE} ${PROJECT_NAME}.hex
        COMMAND arm-none-eabi-objcopy -O binary ${EXECUTABLE} ${PROJECT_NAME}.bin)
