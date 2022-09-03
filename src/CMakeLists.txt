set(lib_name ${PROJECT_NAME})
########################################
set(${lib_name}_libsrcs
  auth.c
  common.h
  conn.c
  crypto.c
  ctx.c
  event.c
  handler.c
  hash.c
  hash.h
  jid.c
  md5.c
  md5.h
  ostypes.h
  parser_expat.c
  parser.h
  #parser_libxml2.c # parser_expat instead
  rand.c
  rand.h
  resolver.c
  resolver.h
  sasl.c
  sasl.h
  scram.c
  scram.h
  sha1.c
  sha1.h
  snprintf.c
  snprintf.h
  sock.c
  sock.h
  stanza.c
  #tls_dummy.c # tls_openssl instead
  #tls_gnutls.c # tls_openssl instead
  tls.h
  tls_openssl.c
  #tls_schannel.c # tls_openssl instead
  util.c
  util.h
  uuid.c
  )
source_group("" FILES ${${lib_name}_libsrcs})
########################################
add_library(${lib_name} STATIC ${${lib_name}_libsrcs})
target_include_directories(${lib_name} PUBLIC $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}> $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  )
target_link_libraries(${lib_name} PRIVATE ${EXPAT_LIBRARIES} ${OPENSSL_LIBRARIES} ${SYS_LIBS})
set_target_properties(${lib_name} PROPERTIES
  PREFIX "" # strip off the "lib" prefix, since it's already libstrophe
  )
########################################
set(targetsFile ${PROJECT_NAME}-targets)
install(TARGETS ${lib_name} EXPORT ${targetsFile}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  )
install(EXPORT ${targetsFile} DESTINATION ${XP_INSTALL_CMAKEDIR} ${nameSpace})
