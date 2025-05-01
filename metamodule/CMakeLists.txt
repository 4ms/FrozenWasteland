cmake_minimum_required(VERSION 3.22)

if(NOT "${METAMODULE_SDK_DIR}" STREQUAL "")
	message("METAMODULE_SDK_DIR set by CMake variable ${METAMODULE_SDK_DIR}")
elseif (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
	message("METAMODULE_SDK_DIR set by environment variable ${METAMODULE_SDK_DIR}")
else()
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
	message("METAMODULE_SDK_DIR set to default: ${METAMODULE_SDK_DIR}")
endif()

include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(FrozenWastelandMetaModulePlugin
    DESCRIPTION "FrozenWasteland Plugin for MetaModule" 
    LANGUAGES   C CXX ASM
)

add_library(FrozenWasteland STATIC)

set(SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/../src)


file(GLOB FWSOURCES ${SOURCE_DIR}/*.cpp)

target_sources(FrozenWasteland
    PRIVATE
	${FWSOURCES}
	${SOURCE_DIR}/dsp-compressor/SimpleComp.cpp
	${SOURCE_DIR}/dsp-compressor/SimpleEnvelope.cpp
	${SOURCE_DIR}/dsp-compressor/SimpleGate.cpp
	${SOURCE_DIR}/dsp-compressor/SimpleLimit.cpp
	${SOURCE_DIR}/dsp-filter/third-party/falco/DspFilter.cpp
	${SOURCE_DIR}/dsp-noise/noise.cpp
	${SOURCE_DIR}/filters/biquad.cpp
	${SOURCE_DIR}/ui/menu.cpp
	${SOURCE_DIR}/xml/tinyxml2.cpp
)

target_include_directories(FrozenWasteland
	PRIVATE
	${SOURCE_DIR}/xml
	${SOURCE_DIR}/ui
	${SOURCE_DIR}/model
	${SOURCE_DIR}/dsp-compressor
	${SOURCE_DIR}/dsp-delay
	${SOURCE_DIR}/dsp-filter/utils 
	${SOURCE_DIR}/dsp-filter/filters 
	${SOURCE_DIR}/dsp-filter/third-party/falco
)

set_property(TARGET FrozenWasteland PROPERTY CXX_STANDARD 23)

if("${INSTALL_DIR}" STREQUAL "")
    set(INSTALL_DIR ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins)
endif()

create_plugin(
    SOURCE_LIB      FrozenWasteland                                          # This is the cmake target name (defined in add_target)
    PLUGIN_NAME     FrozenWasteland                                          # This must match the brand "slug" used in VCV Rack
    PLUGIN_JSON     ${CMAKE_CURRENT_LIST_DIR}/../plugin.json                      # Path to the plugin.json file used by VCV Rack
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets               # Path to the assets/ dir containing the PNGs
    DESTINATION     ${INSTALL_DIR}                                 # Dir where plugin .mmplugin file should be put
)

