# STM32F446RETX Microcontroller

## Moved to new location: https://gitlab.com/pab44/stm32-f446re-adc-uart-data-transfer

This is firmware for the STM32-F446RETX microcontroller that is also found in Nucleo-F446RE development board. 

It reads from 8 ADC inputs, uses DMA to store the digital values into the buffer, and transfers data to PC using the USART UART bridge.

#Steps to Build and Upload Firmware (for Windows)

1. mkdir build-debug
2. cd build-debug
3. cmake -G "MinGW Makefiles" -DCMAKE_TOOLCHAIN_FILE=../arm-none-eabi-gcc.cmake -DCMAKE_BUILD_TYPE=Debug ..
4. cmake --build . -- -j 4
5. Upload .hex file to microcontroller by using STM32Programmer software.

#Steps to Build and Upload Firmware (for Linux/Unix)

1. mkdir build-debug
2. cd build-debug
3. cmake -DCMAKE_TOOLCHAIN_FILE=../arm-none-eabi-gcc.cmake -DCMAKE_BUILD_TYPE=Debug ..
4. cmake --build . -- -j 4
5. Upload .hex file to microcontroller by using STM32Programmer software.
