This project is forked from 2.5.0 SQLite with support for Cmake build system
    SQLite: An SQL Database Engine in a C Library

## Pre-requistics: 
1)  Make Sure You Have [Cmake](https://cmake.org/download/) Installed
2)  Install [Make for Windows](https://gnuwin32.sourceforge.net/packages/make.htm) from GNU Tool Chain 
3) Install C Compiler for Windows , I prefer [TDM-GCC](https://jmeubank.github.io/tdm-gcc/)

## How to Generate Build Files
```
cmake -S . -B "build" -G "Unix Makefiles" 
```
![image](https://github.com/user-attachments/assets/89a62972-1c53-46d8-807f-9104c50a20bc)


## How to Build 
Navigate to Build Folder
```
cmake --build . 
```
![image](https://github.com/user-attachments/assets/cd3f89bf-ad25-44c0-a19b-833d2779de98)



