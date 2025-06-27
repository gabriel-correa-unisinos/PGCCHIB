cmake_minimum_required(VERSION 3.10)
project(PGCCHIB)

# Define o padrão do C++
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Ativa o FetchContent
include(FetchContent)

# Configura o FetchContent para baixar e compilar GLFW automaticamente
FetchContent_Declare(
  glfw
  GIT_REPOSITORY https://github.com/glfw/glfw.git
  GIT_TAG 3.4  # Define a versão desejada da GLFW
)

# Configura o FetchContent para baixar a GLM automaticamente
FetchContent_Declare(
  glm
  GIT_REPOSITORY https://github.com/g-truc/glm.git
  GIT_TAG master  # Define a versão desejada da GLM
)

# Faz o download e compila as bibliotecas
FetchContent_MakeAvailable(glfw glm)

# Configura o FetchContent para baixar a stb_image automaticamente
FetchContent_Declare(
  stb_image
  GIT_REPOSITORY https://github.com/nothings/stb.git
  GIT_TAG master
)

FetchContent_MakeAvailable(stb_image)

# Adiciona as pastas de cabeçalhos
include_directories(${CMAKE_SOURCE_DIR}/include)
include_directories(${CMAKE_SOURCE_DIR}/common)
include_directories(${CMAKE_SOURCE_DIR}/common/M5-6)
include_directories(${CMAKE_SOURCE_DIR}/include/glad)
include_directories(${glm_SOURCE_DIR})


# Lista de exemplos/exercícios podem ser colocados aqui também
set(EXERCISES
    ExemplosMoodle/M1_material/exemplo_00
    ExemplosMoodle/M1_material/exemplo_01
    ExemplosMoodle/M2_material/exemplo_02
    ExemplosMoodle/M3_material/exemplo_03
    ExemplosMoodle/M4_material/exemplo_04
    # ExemplosMoodle/M5_material/exemplo_05
    HelloTriangle
    HelloTransform
    HelloTexture
    Modulo2/Ex1Parte1M2
    Modulo2/Ex1Parte2M2
    Modulo2/Ex1Parte3M2
    Modulo3/M3JogoCores
    Modulo4/TextureObject
    #Vivencial_3/TilemapIsometric
    Prova_GB/GrauB
    AplicacaodeTransformacao/Ex1
    AplicacaodeTransformacao/Ex2
    AplicacaodeTransformacao/Ex3
)

add_compile_options(-Wno-pragmas)

# Define as bibliotecas para cada sistema operacional
if(WIN32)
    set(OPENGL_LIBS opengl32)
elseif(APPLE)
    find_library(OpenGL_LIBRARY OpenGL)
    set(OPENGL_LIBS ${OpenGL_LIBRARY})
else()
    find_package(OpenGL REQUIRED)
    set(OPENGL_LIBS ${OPENGL_gl_LIBRARY})
endif()

# Caminho esperado para a GLAD
set(GLAD_C_FILE "${CMAKE_SOURCE_DIR}/common/glad.c")

# Verifica se os arquivos da GLAD estão no lugar
if (NOT EXISTS ${GLAD_C_FILE})
    message(FATAL_ERROR "Arquivo glad.c não encontrado! Baixe a GLAD manualmente em https://glad.dav1d.de/ e coloque glad.h em include/glad/ e glad.c em common/")
endif()

# Cria os executáveis
foreach(EXERCISE ${EXERCISES})
    get_filename_component(EXE_NAME ${EXERCISE} NAME)

    set(SOURCE_FILES src/${EXERCISE}.cpp ${GLAD_C_FILE})

    # Se for o TextureObject, adiciona arquivos extras
    if(${EXE_NAME} STREQUAL "TextureObject")
        list(APPEND SOURCE_FILES
            src/Modulo4/Shader.cpp
            src/Modulo4/Sprite.cpp
        )
    endif()

    add_executable(${EXE_NAME} ${SOURCE_FILES})

    target_include_directories(${EXE_NAME} PRIVATE
        ${CMAKE_SOURCE_DIR}/include/glad
        ${CMAKE_SOURCE_DIR}/include
        ${CMAKE_SOURCE_DIR}/common
        ${CMAKE_SOURCE_DIR}/common/M5-6
        ${glm_SOURCE_DIR}
        ${stb_image_SOURCE_DIR}
    )

    target_link_libraries(${EXE_NAME} glfw ${OPENGL_LIBS} glm::glm)
endforeach()
