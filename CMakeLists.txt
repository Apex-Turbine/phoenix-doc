# Copy phoenix-doc (including Components/Images) to the build directory under doc
file(
    COPY ${CMAKE_CURRENT_SOURCE_DIR}/
    DESTINATION ${CMAKE_BINARY_DIR}/doc/phoenix-doc
)

# Install phoenix-doc (including Components/Images) alongside the install directory under doc
install(
    DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/
    DESTINATION ${PHOENIX_INSTALL_ROOT}/doc/phoenix-doc
)