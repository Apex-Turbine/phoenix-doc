# Copy phoenix-doc to the build directory under doc
file(
    COPY ${CMAKE_CURRENT_SOURCE_DIR}/
    DESTINATION ${CMAKE_BINARY_DIR}/doc/phoenix-doc
)

# Copy assets to the build directory under doc/phoenix-doc/Images
file(
    COPY ${CMAKE_CURRENT_SOURCE_DIR}/.gitbook/assets/
    DESTINATION ${CMAKE_BINARY_DIR}/doc/phoenix-doc/Components/Images
)

# Install phoenix-doc alongside the install directory under doc
install(
    DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/
    DESTINATION ${PHOENIX_INSTALL_ROOT}/doc/phoenix-doc
)

# Install assets alongside the install directory under doc/phoenix-doc/Images
install(
    DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/.gitbook/assets/
    DESTINATION ${PHOENIX_INSTALL_ROOT}/doc/phoenix-doc/Components/Images
)