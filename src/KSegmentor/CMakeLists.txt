file( GLOB ksegm_sources src/*.cxx   src/*.cpp )
file( GLOB ksegm_headers include/*.h  )

# MACOSX_BUNDLE

set( KSEGM_LIBS
  ${OpenCV_LIBS}
  ${VTK_LIBRARIES}
#  vtkRendering  vtkGraphics
#  vtkHybrid vtkImaging vtkIO
#  vtkFiltering vtkCommon
)


add_library(KSegmentor STATIC ${ksegm_headers} ${ksegm_sources})
target_link_libraries(KSegmentor   sfm_local_chanvese   ${KSEGM_LIBS} )

set(KSlice_Angry FALSE CACHE BOOL FALSE) #dont want errors if user doesnt have Boost installed
if(KSlice_Angry)
  add_subdirectory(tests)
endif()


