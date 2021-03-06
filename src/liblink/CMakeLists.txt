# FindMathematica LibraryLink examples

SET(BASE_PATH "${CMAKE_SOURCE_DIR}")
SET(EXT_PATH "${BASE_PATH}/ext")

#Bring the headers into the project
include_directories(SYSTEM
                    ${Mathematica_INCLUDE_DIRS}
                    ${BASE_PATH}/src/optdet/
                    ${EXT_PATH}
                    ${EXT_PATH}/cudd-3.0.0/cudd
                    ${EXT_PATH}/cudd-3.0.0/cplusplus
                    ${EXT_PATH}/cudd-3.0.0/dddmp
                    ${EXT_PATH}/SCOTSv2.0/src
                    ${EXT_PATH}/SCOTSv2.0/utils
                    ${EXT_PATH}/tclap
                    ${EXT_PATH}/svgDrawer)
link_directories(/opt/local/lib /usr/local/lib)

#Add the required flags
set(CMAKE_CXX_FLAGS "-Wall -Wextra -std=c++11 -O3 -DNDEBUG -DSCOTS_BDD")

set(_Sources scots2int.cc)

if (MSVC14)
 	# demo_managed requires hash_map
	add_definitions(-D_SILENCE_STDEXT_HASH_DEPRECATION_WARNINGS)
endif()

# add a CMake shared library target for each LibraryLink example
set (_Targets "")
foreach (_Source ${_Sources})
	if (EXISTS "${CMAKE_CURRENT_BINARY_DIR}/${_Source}")
		file (STRINGS "${CMAKE_CURRENT_BINARY_DIR}/${_Source}" _includesMathLink REGEX ".*#include.*mathlink.h.*")
		file (STRINGS "${CMAKE_CURRENT_BINARY_DIR}/${_Source}" _includesWSTP REGEX ".*#include.*wstp.h.*")

		get_filename_component(_TargetName ${_Source} NAME_WE)
		Mathematica_ADD_LIBRARY (${_TargetName} "${CMAKE_CURRENT_BINARY_DIR}/${_Source}")
		if (_includesMathLink)
			target_link_libraries(${_TargetName} ${Mathematica_MathLink_LIBRARIES})
			if (Mathematica_MathLink_LINKER_FLAGS)
				set_target_properties(${_TargetName} PROPERTIES LINK_FLAGS "${Mathematica_MathLink_LINKER_FLAGS}")
			endif()
		endif()
		if (_includesWSTP)
			target_link_libraries(${_TargetName} ${Mathematica_WSTP_LIBRARIES})
			if (Mathematica_WSTP_LINKER_FLAGS)
				set_target_properties(${_TargetName} PROPERTIES LINK_FLAGS "${Mathematica_WSTP_LINKER_FLAGS}")
			endif()
		endif()
		if (_includesMathLink OR _includesWSTP)
			Mathematica_ABSOLUTIZE_LIBRARY_DEPENDENCIES(${_TargetName})
		endif()
		set_target_properties(${_TargetName} PROPERTIES FOLDER "LibraryLink")
		list (APPEND _Targets ${_TargetName})
		
		#Add the CUDD as a target link library
		target_link_libraries(${_TargetName} cudd)
	endif()
endforeach()

#
#Installing the shared library does not really work, at least it did not no Mac OS X
#
#message(STATUS "Mathematica_USERBASE_DIR = ${Mathematica_USERBASE_DIR}")
#
#if (DEFINED Mathematica_USERBASE_DIR)
#	foreach (_systemID ${Mathematica_SYSTEM_IDS})
#		SET(LibDestinationDir "${Mathematica_USERBASE_DIR}/SystemFiles/LibraryResources/${_systemID}")
#		message(STATUS "Installing ${_Targets} into ${LibDestinationDir}")
#		install(TARGETS ${_Targets} LIBRARY DESTINATION "${LibDestinationDir}" CONFIGURATIONS "Release")
#	endforeach()
#endif()
#
