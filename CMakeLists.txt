cmake_minimum_required(VERSION 3.10)
set(CMAKE_DISABLE_SOURCE_CHANGES ON)
set(CMAKE_DISABLE_IN_SOURCE_BUILD ON)

project(q2pro C)

set(CMAKE_C_STANDARD 99)
#set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

add_definitions(-UNDEBUG)

set(defs-ok
    TESTS
    FPS
    SYSCON
    MVD_CLIENT
    MVD_SERVER
    MAPCHECKSUM
    GLES
    OPENAL
    FIXED_LIBAL
    CLIENT_GTV
    AUTOREPLY

    CURL
    TGA
    PNG
    JPG
    ZLIB

    REF
    UI
    DLIGHTS
    MD3

    AC_SERVER

    CLIENT
    SERVER
)

set(defs-win32 SNDDMA DSOUND DBGHELP)
set(defs-unix "")
set(defs-linux ICMP)

if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    list(APPEND defs-ok ${defs-linux})
endif()

if(WIN32)
    list(APPEND defs-ok ${defs-win32})
else()
    list(APPEND defs-ok ${defs-unix})
endif()

if(CMAKE_COMPILER_IS_GNUCC) # includes clang
    if(CMAKE_C_COMPILER_ID STREQUAL "Clang")
        if (NOT CMAKE_AR)
            set(CMAKE_AR llvm-ar CACHE FILEPATH "" FORCE)
        endif()
        if (NOT CMAKE_RANLIB)
            set(CMAKE_RANLIB llvm-ranlib CACHE FILEPATH "" FORCE)
        endif()
    else() # assume GNU
        if(NOT CMAKE_BUILD_TYPE) # we need -O3 for proper GNU CC warnings
            set(CMAKE_BUILD_TYPE "RELEASE" CACHE STRING "" FORCE)
        endif()

        if (NOT CMAKE_AR)
            set(CMAKE_AR gcc-ar CACHE FILEPATH "" FORCE)
        endif()
        if (NOT CMAKE_RANLIB)
            set(CMAKE_RANLIB gcc-ranlib CACHE FILEPATH "" FORCE)
        endif()
    endif()

    add_definitions(-D_FORTIFY_SOURCE=3 -UNDEBUG)
    add_definitions(-Wall -Wextra -Wstrict-aliasing=3)

    set(noisy-warning-list
        unused-parameter
        sign-compare
        missing-field-initializers
        strict-overflow clobbered attributes
        cast-function-type
    )

    foreach(k ${noisy-warning-list})
        add_definitions(-Wno-${k})
    endforeach()
endif()

set(files-plat-common-win32
    src/windows/hunk.c
    )

set(files-plat-win32
    #src/windows/ac.c
    src/windows/client.c
    src/windows/debug.c
    src/windows/dsound.c
    src/windows/glimp.c
    src/windows/system.c
    src/windows/wave.c
    src/windows/wgl.c
    )

set(files-plat-common-unix
    src/unix/hunk.c
    )

set(files-plat-unix
    #src/unix/oss.c
    src/unix/sound.c
    src/unix/tty.c
    src/unix/video.c
)

if(WIN32)
    set(files-plat ${files-plat-win32})
    set(files-plat-common ${files-plat-common-win32})
else()
    set(files-plat ${files-plat-unix})
    set(files-plat-common ${files-plat-common-unix})
endif()

set(files-common
    src/common/net/chan.c
    src/common/net/net.c
    src/common/bsp.c
    src/common/cmodel.c
    src/common/cvar.c
    src/common/error.c
    src/common/field.c
    src/common/fifo.c
    src/common/math.c
    src/common/mdfour.c
    src/common/msg.c
    src/common/pmove.c
    src/common/sizebuf.c
    src/common/utils.c
    src/common/zone.c
    src/shared/shared.c
    src/shared/m_flash.c
)

set(files
    src/common/cmd.c
    src/common/common.c
    src/common/prompt.c
    src/common/files.c
    src/common/tests.c
    #src/client/sound/qal/dynamic.c
    #src/client/sound/qal/dynamic.h
    src/client/sound/qal/fixed.c

    src/client/sound/al.c
    src/client/sound/dma.c
    src/client/sound/main.c
    src/client/sound/mem.c
    src/client/sound/mix.c
    src/client/ui/demos.c
    src/client/ui/menu.c
    src/client/ui/playerconfig.c
    src/client/ui/playermodels.c
    src/client/ui/script.c
    src/client/ui/servers.c
    src/client/ui/ui.c
    src/client/ascii.c
    src/client/console.c
    src/client/crc.c
    src/client/demo.c
    src/client/download.c
    src/client/effects.c
    src/client/entities.c
    src/client/gtv.c
    src/client/http.c
    src/client/input.c
    src/client/keys.c
    src/client/locs.c
    src/client/main.c
    src/client/newfx.c
    src/client/parse.c
    src/client/precache.c
    src/client/predict.c
    src/client/refresh.c
    src/client/screen.c
    src/client/tent.c
    src/client/view.c

    src/refresh/draw.c
    src/refresh/hq2x.c
    src/refresh/images.c
    src/refresh/legacy.c
    src/refresh/main.c
    src/refresh/mesh.c
    src/refresh/models.c
    src/refresh/qgl.c
    src/refresh/shader.c
    src/refresh/sky.c
    src/refresh/state.c
    src/refresh/surf.c
    src/refresh/tess.c
    src/refresh/texture.c
    src/refresh/world.c
    src/server/mvd/client.c
    src/server/mvd/game.c
    src/server/mvd/parse.c
    src/server/ac.c
    src/server/commands.c
    src/server/entities.c
    src/server/game.c
    src/server/init.c
    src/server/main.c
    src/server/mvd.c
    src/server/save.c
    src/server/send.c
    src/server/user.c
    src/server/world.c
    ${files-plat}
    ${files-common}
    ${files-plat-common}
)

set(baseq2-files
    src/shared/shared.c
    src/shared/m_flash.c
    src/baseq2/g_ai.c
    src/baseq2/g_chase.c
    src/baseq2/g_cmds.c
    src/baseq2/g_combat.c
    src/baseq2/g_func.c
    src/baseq2/g_items.c
    src/baseq2/g_main.c
    src/baseq2/g_misc.c
    src/baseq2/g_monster.c
    src/baseq2/g_phys.c
    src/baseq2/g_ptrs.c
    src/baseq2/g_save.c
    src/baseq2/g_spawn.c
    src/baseq2/g_svcmds.c
    src/baseq2/g_target.c
    src/baseq2/g_trigger.c
    src/baseq2/g_turret.c
    src/baseq2/g_utils.c
    src/baseq2/g_weapon.c
    src/baseq2/m_actor.c
    src/baseq2/m_berserk.c
    src/baseq2/m_boss2.c
    src/baseq2/m_boss31.c
    src/baseq2/m_boss32.c
    src/baseq2/m_boss3.c
    src/baseq2/m_brain.c
    src/baseq2/m_chick.c
    src/baseq2/m_flipper.c
    src/baseq2/m_float.c
    src/baseq2/m_flyer.c
    src/baseq2/m_gladiator.c
    src/baseq2/m_gunner.c
    src/baseq2/m_hover.c
    src/baseq2/m_infantry.c
    src/baseq2/m_insane.c
    src/baseq2/m_medic.c
    src/baseq2/m_move.c
    src/baseq2/m_mutant.c
    src/baseq2/m_parasite.c
    src/baseq2/m_soldier.c
    src/baseq2/m_supertank.c
    src/baseq2/m_tank.c
    src/baseq2/p_client.c
    src/baseq2/p_hud.c
    src/baseq2/p_trail.c
    src/baseq2/p_view.c
    src/baseq2/p_weapon.c
)

include_directories(inc)

if(CMAKE_SYSTEM_PROCESSOR MATCHES "^(AMD64|amd64)$")
    set(plat "x86_64")
elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "^i.86$")
    set(plat "x86")
else() # rest of the names seem to be reasonable
    string(TOLOWER "${CMAKE_SYSTEM_PROCESSOR}" plat)
endif()

add_library("game${plat}" SHARED ${baseq2-files})
add_executable(q2pro ${files})

set_property(TARGET "game${plat}" PROPERTY PREFIX "")

foreach(k ${defs-ok})
    target_compile_definitions(q2pro PRIVATE -DUSE_${k}=1)
endforeach()

target_compile_definitions(q2pro PRIVATE
    -DREVISION=\"\" -DCPUSTRING=\"${plat}\" -DBUILDSTRING=\"build\" -DVERSION=\"HEAD\"
    -DBASEGAME=\"baseq2\" -DDEFGAME=\"\"
    -DVID_GEOMETRY=\"desktop\" -DVID_MODELIST=\"desktop\" -DVID_REF=\"gl\"
    -D_DEBUG=1
    #-DAL_LIBTYPE_STATIC
)

target_link_libraries(q2pro png jpeg curl openal z)

if(WIN32)
    target_link_libraries(q2pro dsound winmm ws2_32)
else()
    target_compile_definitions(q2pro -DUSE_SDL=2)
    target_link_libraries(q2pro SDL2 m rt)
    target_link_libraries(openal)
endif()

add_custom_target(empty)
