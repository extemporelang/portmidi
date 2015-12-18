cmake_minimum_required(VERSION 3.1) # we use target_sources()

############
# portmidi #
############

add_library(portmidi SHARED
  pm_common/portmidi.c
  pm_common/pmutil.c
  porttime/porttime.c
  )
target_include_directories(portmidi
  PRIVATE pm_common
  PRIVATE porttime
  )
target_compile_options(portmidi
  PRIVATE -Wno-deprecated-declarations)
target_link_libraries(portmidi
  PRIVATE "-framework CoreFoundation"
  PRIVATE "-framework CoreAudio"
  PRIVATE "-framework CoreMIDI"
  PRIVATE "-framework Carbon" # for readbinaryplist.c
  )

# platform-specific

if(APPLE)
  target_sources(portmidi
    PRIVATE porttime/ptmacosx_mach.c
    PRIVATE pm_mac/finddefault.c
    PRIVATE pm_mac/pmmac.c
    PRIVATE pm_mac/pmmacosxcm.c
    PRIVATE pm_mac/readbinaryplist.c
    )
  target_include_directories(portmidi
    PRIVATE pm_mac
    )
elseif(UNIX AND NOT APPLE)
  target_sources(portmidi
    PRIVATE porttime/ptlinux.c
    PRIVATE pm_linux/finddefault.c
    PRIVATE pm_linux/pmlinux.c
    PRIVATE pm_linux/pmlinuxalsa.c
    )
  target_include_directories(portmidi
    PRIVATE pm_linux
    )
  target_compile_definitions(portmidi
    PRIVATE -DPMALSA)
elseif(WIN32)
  target_sources(portmidi
    PRIVATE porttime/ptwinmm.c
    PRIVATE pm_win/pmwin.c
    PRIVATE pm_win/pmwinmm.c
    )
  target_include_directories(portmidi
    PRIVATE pm_linux
    )
endif()

install(TARGETS portmidi
  DESTINATION lib)
