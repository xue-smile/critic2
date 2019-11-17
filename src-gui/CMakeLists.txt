set(CMAKE_CXX_STANDARD 11)
add_definitions(-D_GLIBCXX_USE_CXX11_ABI=1)

set(SOURCES dialog.cpp keybinding.cpp main.cpp menu.cpp message.cpp
  scene.cpp settings.cpp shapes.cpp text.cpp tree.cpp view.cpp
  imgui/imgui.cpp imgui/imgui_demo.cpp imgui/imgui_dock.cpp 
  imgui/imgui_draw.cpp imgui/imgui_impl_glfw_gl3.cpp imgui/imgui_widgets.cpp
  imgui/gl3w.c
  )

find_package(PkgConfig REQUIRED)
pkg_search_module(GLFW REQUIRED glfw3)
pkg_search_module(FREETYPE2 REQUIRED freetype2)
pkg_search_module(GLM REQUIRED glm)
pkg_search_module(GL REQUIRED gl)

add_executable(gcritic2 ${SOURCES} 
  $<TARGET_OBJECTS:lcritic2> 
  $<TARGET_OBJECTS:cubpack>
  $<TARGET_OBJECTS:ciftbx> 
  )
target_include_directories(gcritic2 PRIVATE
  ${GLFW_INCLUDE_DIRS} 
  ${FREETYPE2_INCLUDE_DIRS} 
  ${GLM_INCLUDE_DIRS}
  ${GL_INCLUDE_DIRS}
  )
target_link_libraries(gcritic2 PRIVATE
  ${GLFW_LIBRARIES} 
  ${FREETYPE2_LIBRARIES} 
  ${GLM_LIBRARIES} 
  ${GL_LIBRARIES}
  OpenMP::OpenMP_Fortran
  ${CMAKE_DL_LIBS}
  )
if (LAPACK_FOUND)
  target_link_libraries(gcritic2 PRIVATE ${LAPACK_LIBRARIES})
else()
  target_link_libraries(gcritic2 PRIVATE oldlibs)
endif()
if (QHULL_FOUND)
  target_include_directories(gcritic2 PRIVATE ${QHULL_INCLUDE_DIRS})
  target_link_libraries(gcritic2 PRIVATE ${QHULL_LIBRARIES})
else()
  target_link_libraries(gcritic2 PRIVATE qhull)
endif()
if (SPGLIB_FOUND)
  target_link_libraries(gcritic2 PRIVATE ${SPGLIB_LIBRARIES})
else()
  target_link_libraries(gcritic2 PRIVATE spglib)
endif()

install(TARGETS gcritic2 RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})