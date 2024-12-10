cmake_minimum_required(VERSION 3.10)
project(setup)

# Option to set the installation directory
option(CUSTOM_INSTALL_DIR "Custom installation directory" "/usr/local/bin")
set(INSTALL_DIR "${CUSTOM_INSTALL_DIR}")

# Function to add tool paths to PATH
function(add_tool_to_path TOOL_PATHS)
    foreach(PATH IN LISTS TOOL_PATHS)
        set(ENV{PATH} "$ENV{PATH}:${PATH}")
    endforeach()
endfunction()

# Function to install a tool
function(install_tool TOOL_NAME TOOL_PACKAGE TOOL_PATHS)
    add_tool_to_path("${TOOL_PATHS}")
    find_program(${TOOL_NAME}_FOUND ${TOOL_NAME} PATHS ${TOOL_PATHS})
    
    if(${TOOL_NAME}_FOUND)
        message(STATUS "${TOOL_NAME} found: ${${TOOL_NAME}_FOUND}")
    else()
        message(FATAL_ERROR "${TOOL_NAME} not found in system paths. Install ${TOOL_PACKAGE} manually or update the path.")
    endif()

    install(PROGRAMS ${${TOOL_NAME}_FOUND} DESTINATION ${INSTALL_DIR})
endfunction()

# Install Bash
install_tool("BASH" "bash" "/bin;/usr/bin;/usr/local/bin")

# Install Neovim (nvim)
install_tool("NVIM" "neovim" "/bin;/usr/bin;/usr/local/bin")

# Install Neovim init.lua configuration
install(FILES ${CMAKE_SOURCE_DIR}/init.lua DESTINATION ${INSTALL_DIR})

# Install GCC
install_tool("GCC" "gcc" "/bin;/usr/bin;/usr/local/bin")

# Add a custom target to perform installation
add_custom_target(install_tools
    COMMENT "Installing Bash, Neovim, and GCC"
    COMMAND ${CMAKE_COMMAND} --install ${CMAKE_BINARY_DIR}
)
