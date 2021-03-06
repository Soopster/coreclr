set(CMAKE_INCLUDE_CURRENT_DIR ON)

add_definitions(-DFEATURE_NO_HOST)
add_definitions(-D_TARGET_AMD64_=1)
add_definitions(-DDBG_TARGET_64BIT=1)
add_definitions(-DDBG_TARGET_AMD64=1)
add_definitions(-DDBG_TARGET_WIN64=1)

include_directories(BEFORE ${VM_DIR})
include_directories(BEFORE ${VM_DIR}/${ARCH_SOURCES_DIR})
include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR})

set(CORDBEE_SOURCES_DAC_AND_WKS
  controller.cpp
  debugger.cpp
  debuggermodule.cpp
  functioninfo.cpp
)

set(CORDBEE_SOURCES_WKS
  ${CORDBEE_SOURCES_DAC_AND_WKS}
  funceval.cpp
  rcthread.cpp
  canary.cpp
  shared.cpp
  frameinfo.cpp
  ${ARCH_SOURCES_DIR}/primitives.cpp
  ${ARCH_SOURCES_DIR}/debuggerregdisplayhelper.cpp
)

set(CORDBEE_SOURCES_DAC 
  ${CORDBEE_SOURCES_DAC_AND_WKS}
)

if(WIN32)
  set(CORDBEE_SOURCES_WKS
    ${CORDBEE_SOURCES_WKS}
    # The following files need to be ported to Linux
    inprocdac.cpp
    dbgtransportproxy.cpp
    ddunpack.cpp
  )
endif(WIN32)

if (IS_64BIT_BUILD EQUAL 1)
  set(CORDBEE_SOURCES_WKS ${CORDBEE_SOURCES_WKS} amd64/amd64walker.cpp)
else (IS_64BIT_BUILD EQUAL 1)
  set(CORDBEE_SOURCES_WKS ${CORDBEE_SOURCES_WKS} i386/x86walker.cpp)
endif (IS_64BIT_BUILD EQUAL 1)

convert_to_absolute_path(CORDBEE_SOURCES_DAC ${CORDBEE_SOURCES_DAC})
convert_to_absolute_path(CORDBEE_SOURCES_WKS ${CORDBEE_SOURCES_WKS})

set(CORDBEE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

add_subdirectory(dac)
add_subdirectory(wks)