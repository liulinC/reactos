
PROJECT(NTVDM)

#####################################
# Generate the integrated COMMAND.COM
#

# Retrieve the full path to the generated file of the 'command' target
get_target_property(_command_com_file command LOCATION)

add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/command_com.c ${CMAKE_CURRENT_BINARY_DIR}/command_com.h
    COMMAND native-bin2c ${_command_com_file} ${CMAKE_CURRENT_BINARY_DIR}/command_com.c ${CMAKE_CURRENT_BINARY_DIR}/command_com.h BIN CommandCom
    DEPENDS native-bin2c command ${_command_com_file})
#####################################

include_directories(${REACTOS_SOURCE_DIR}/sdk/include/reactos/libs/fast486)

spec2def(ntvdm.exe ntvdm.spec ADD_IMPORTLIB)

list(APPEND SOURCE
    bios/bios32/bios32.c
    bios/bios32/dskbios32.c
    bios/bios32/kbdbios32.c
    bios/bios32/moubios32.c
    bios/bios32/vbe.c
    bios/bios32/vidbios32.c
    bios/bios.c
    bios/kbdbios.c
    bios/rom.c
    bios/umamgr.c
    bios/vidbios.c
    cpu/bop.c
    cpu/callback.c
    cpu/cpu.c
    cpu/registers.c
    hardware/cmos.c
    hardware/disk.c
    hardware/dma.c
    hardware/keyboard.c
    hardware/mouse.c
    hardware/pic.c
    hardware/pit.c
    hardware/ppi.c
    hardware/ps2.c
    hardware/sound/speaker.c
    hardware/video/svga.c
    dos/dos32krnl/bios.c
    dos/dos32krnl/condrv.c
    dos/dos32krnl/country.c
    dos/dos32krnl/device.c
    dos/dos32krnl/dos.c
    dos/dos32krnl/dosfiles.c
    dos/dos32krnl/emsdrv.c
    dos/dos32krnl/handle.c
    dos/dos32krnl/himem.c
    dos/dos32krnl/memory.c
    dos/dos32krnl/process.c
    dos/dem.c
    dos/mouse32.c
    ${CMAKE_CURRENT_BINARY_DIR}/command_com.c
    clock.c
    emulator.c
    int32.c
    io.c
    memory.c
    utils.c
    vddsup.c
    ntvdm.c
    ntvdm.h
    ${CMAKE_CURRENT_BINARY_DIR}/ntvdm.def)

file(GLOB ntvdm_rc_deps res/*.*)
add_rc_deps(ntvdm.rc ${ntvdm_rc_deps})
add_executable(ntvdm ${SOURCE} ntvdm.rc)
add_pch(ntvdm ntvdm.h SOURCE)
set_module_type(ntvdm win32cui UNICODE IMAGEBASE 0x0F000000)
target_link_libraries(ntvdm fast486 ${PSEH_LIB})
add_importlibs(ntvdm user32 gdi32 advapi32 comdlg32 msvcrt kernel32 ntdll)
add_cd_file(TARGET ntvdm DESTINATION reactos/system32 FOR all)
