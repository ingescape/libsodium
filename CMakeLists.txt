########################################################################
# Project setup
########################################################################

cmake_minimum_required(VERSION 3.16)
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.11" CACHE STRING "Minimum OS X deployment version")

option(OSX_UNIVERSAL "Build for both arm64 and x86_64" OFF)
if(OSX_UNIVERSAL)
    set(CMAKE_OSX_ARCHITECTURES "arm64;x86_64" CACHE STRING "Build universal binaries")
    message(STATUS "Building for arm64 and x86_64")
endif()

project(sodium)

set(SODIUM_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium")

option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

########################################################################
# version
########################################################################

set(SODIUM_LIBRARY_VERSION_MAJOR 10)
set(SODIUM_LIBRARY_VERSION_MINOR 3)
set(SODIUM_LIBRARY_MINIMAL_DEF )
set(VERSION "1.0.18")
message(STATUS "Detected SODIUM Version - ${VERSION}")

########################################################################
# includes
########################################################################

set (sodium_main_headers
    ${SODIUM_SOURCE_DIR}/include/sodium.h
)
set (sodium_headers
	${SODIUM_SOURCE_DIR}/include/sodium/core.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_aead_aes256gcm.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_aead_chacha20poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_aead_xchacha20poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_auth.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_auth_hmacsha256.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_auth_hmacsha512.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_auth_hmacsha512256.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_box.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_box_curve25519xchacha20poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_box_curve25519xsalsa20poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_core_ed25519.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_core_hchacha20.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_core_hsalsa20.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_core_ristretto255.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_core_salsa20.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_core_salsa2012.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_core_salsa208.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_generichash.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_generichash_blake2b.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_hash.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_hash_sha256.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_hash_sha512.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_kdf.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_kdf_blake2b.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_kx.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_onetimeauth.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_onetimeauth_poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_pwhash.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_pwhash_argon2i.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_pwhash_argon2id.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_pwhash_scryptsalsa208sha256.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_scalarmult.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_scalarmult_curve25519.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_scalarmult_ed25519.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_scalarmult_ristretto255.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_secretbox.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_secretbox_xchacha20poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_secretbox_xsalsa20poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_secretstream_xchacha20poly1305.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_shorthash.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_shorthash_siphash24.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_sign.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_sign_ed25519.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_sign_edwards25519sha512batch.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_stream.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_stream_chacha20.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_stream_salsa20.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_stream_salsa2012.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_stream_salsa208.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_stream_xchacha20.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_stream_xsalsa20.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_verify_16.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_verify_32.h
	${SODIUM_SOURCE_DIR}/include/sodium/crypto_verify_64.h
	${SODIUM_SOURCE_DIR}/include/sodium/export.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/chacha20_ietf_ext.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/common.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/ed25519_ref10.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/ed25519_ref10_fe_25_5.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/ed25519_ref10_fe_51.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/implementations.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/mutex.h
	${SODIUM_SOURCE_DIR}/include/sodium/private/sse2_64_32.h
	${SODIUM_SOURCE_DIR}/include/sodium/randombytes.h
	${SODIUM_SOURCE_DIR}/include/sodium/randombytes_internal_random.h
	${SODIUM_SOURCE_DIR}/include/sodium/randombytes_sysrandom.h
	${SODIUM_SOURCE_DIR}/include/sodium/runtime.h
	${SODIUM_SOURCE_DIR}/include/sodium/utils.h
	${SODIUM_SOURCE_DIR}/include/sodium/version.h
)

include(GNUInstallDirs)

install(FILES ${sodium_headers} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/sodium)
install(FILES ${sodium_main_headers} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

########################################################################
# library
########################################################################

set(PUBLIC_INCLUDE_DIR "${SODIUM_SOURCE_DIR}/include" "${SODIUM_SOURCE_DIR}/include/sodium")
set(PUBLIC_INCLUDE_DIR_FOR_INSTALL "include" "include/sodium")

set(PRIVATE_INCLUDE_DIR "${SODIUM_SOURCE_DIR}/crypto_core" "${SODIUM_SOURCE_DIR}/crypto_generichash" "${SODIUM_SOURCE_DIR}/crypto_onetimeauth" "${SODIUM_SOURCE_DIR}/crypto_pwhash" "${SODIUM_SOURCE_DIR}/crypto_scalarmult" "${SODIUM_SOURCE_DIR}/crypto_shorthash" "${SODIUM_SOURCE_DIR}/crypto_sign" "${SODIUM_SOURCE_DIR}/crypto_stream")


set (sodium_sources
    ${SODIUM_SOURCE_DIR}/crypto_aead/aes256gcm/aesni/aead_aes256gcm_aesni.c
	${SODIUM_SOURCE_DIR}/crypto_aead/chacha20poly1305/sodium/aead_chacha20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_aead/xchacha20poly1305/sodium/aead_xchacha20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_auth/crypto_auth.c
	${SODIUM_SOURCE_DIR}/crypto_auth/hmacsha256/auth_hmacsha256.c
	${SODIUM_SOURCE_DIR}/crypto_auth/hmacsha512/auth_hmacsha512.c
	${SODIUM_SOURCE_DIR}/crypto_auth/hmacsha512256/auth_hmacsha512256.c
	${SODIUM_SOURCE_DIR}/crypto_box/crypto_box.c
	${SODIUM_SOURCE_DIR}/crypto_box/crypto_box_easy.c
	${SODIUM_SOURCE_DIR}/crypto_box/crypto_box_seal.c
	${SODIUM_SOURCE_DIR}/crypto_box/curve25519xchacha20poly1305/box_curve25519xchacha20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_box/curve25519xchacha20poly1305/box_seal_curve25519xchacha20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_box/curve25519xsalsa20poly1305/box_curve25519xsalsa20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_core/ed25519/core_ed25519.c
	${SODIUM_SOURCE_DIR}/crypto_core/ed25519/core_ristretto255.c
	${SODIUM_SOURCE_DIR}/crypto_core/ed25519/ref10/ed25519_ref10.c
	${SODIUM_SOURCE_DIR}/crypto_core/hchacha20/core_hchacha20.c
	${SODIUM_SOURCE_DIR}/crypto_core/hsalsa20/core_hsalsa20.c
	${SODIUM_SOURCE_DIR}/crypto_core/hsalsa20/ref2/core_hsalsa20_ref2.c
	${SODIUM_SOURCE_DIR}/crypto_core/salsa/ref/core_salsa_ref.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/blake2b/generichash_blake2.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/blake2b/ref/blake2b-compress-avx2.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/blake2b/ref/blake2b-compress-ref.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/blake2b/ref/blake2b-compress-sse41.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/blake2b/ref/blake2b-compress-ssse3.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/blake2b/ref/blake2b-ref.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/blake2b/ref/generichash_blake2b.c
	${SODIUM_SOURCE_DIR}/crypto_generichash/crypto_generichash.c
	${SODIUM_SOURCE_DIR}/crypto_hash/crypto_hash.c
	${SODIUM_SOURCE_DIR}/crypto_hash/sha256/cp/hash_sha256_cp.c
	${SODIUM_SOURCE_DIR}/crypto_hash/sha256/hash_sha256.c
	${SODIUM_SOURCE_DIR}/crypto_hash/sha512/cp/hash_sha512_cp.c
	${SODIUM_SOURCE_DIR}/crypto_hash/sha512/hash_sha512.c
	${SODIUM_SOURCE_DIR}/crypto_kdf/blake2b/kdf_blake2b.c
	${SODIUM_SOURCE_DIR}/crypto_kdf/crypto_kdf.c
	${SODIUM_SOURCE_DIR}/crypto_kx/crypto_kx.c
	${SODIUM_SOURCE_DIR}/crypto_onetimeauth/crypto_onetimeauth.c
	${SODIUM_SOURCE_DIR}/crypto_onetimeauth/poly1305/donna/poly1305_donna.c
	${SODIUM_SOURCE_DIR}/crypto_onetimeauth/poly1305/onetimeauth_poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_onetimeauth/poly1305/sse2/poly1305_sse2.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/argon2-core.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/argon2-encoding.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/argon2-fill-block-avx2.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/argon2-fill-block-avx512f.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/argon2-fill-block-ref.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/argon2-fill-block-ssse3.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/argon2.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/blake2b-long.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/pwhash_argon2i.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/argon2/pwhash_argon2id.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/crypto_pwhash.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/scryptsalsa208sha256/crypto_scrypt-common.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/scryptsalsa208sha256/nosse/pwhash_scryptsalsa208sha256_nosse.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/scryptsalsa208sha256/pbkdf2-sha256.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/scryptsalsa208sha256/pwhash_scryptsalsa208sha256.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/scryptsalsa208sha256/scrypt_platform.c
	${SODIUM_SOURCE_DIR}/crypto_pwhash/scryptsalsa208sha256/sse/pwhash_scryptsalsa208sha256_sse.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/crypto_scalarmult.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/curve25519/ref10/x25519_ref10.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/curve25519/sandy2x/curve25519_sandy2x.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/curve25519/sandy2x/fe51_invert.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/curve25519/sandy2x/fe_frombytes_sandy2x.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/curve25519/scalarmult_curve25519.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/ed25519/ref10/scalarmult_ed25519_ref10.c
	${SODIUM_SOURCE_DIR}/crypto_scalarmult/ristretto255/ref10/scalarmult_ristretto255_ref10.c
	${SODIUM_SOURCE_DIR}/crypto_secretbox/crypto_secretbox.c
	${SODIUM_SOURCE_DIR}/crypto_secretbox/crypto_secretbox_easy.c
	${SODIUM_SOURCE_DIR}/crypto_secretbox/xchacha20poly1305/secretbox_xchacha20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_secretbox/xsalsa20poly1305/secretbox_xsalsa20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_secretstream/xchacha20poly1305/secretstream_xchacha20poly1305.c
	${SODIUM_SOURCE_DIR}/crypto_shorthash/crypto_shorthash.c
	${SODIUM_SOURCE_DIR}/crypto_shorthash/siphash24/ref/shorthash_siphash24_ref.c
	${SODIUM_SOURCE_DIR}/crypto_shorthash/siphash24/ref/shorthash_siphashx24_ref.c
	${SODIUM_SOURCE_DIR}/crypto_shorthash/siphash24/shorthash_siphash24.c
	${SODIUM_SOURCE_DIR}/crypto_shorthash/siphash24/shorthash_siphashx24.c
	${SODIUM_SOURCE_DIR}/crypto_sign/crypto_sign.c
	${SODIUM_SOURCE_DIR}/crypto_sign/ed25519/ref10/keypair.c
	${SODIUM_SOURCE_DIR}/crypto_sign/ed25519/ref10/obsolete.c
	${SODIUM_SOURCE_DIR}/crypto_sign/ed25519/ref10/open.c
	${SODIUM_SOURCE_DIR}/crypto_sign/ed25519/ref10/sign.c
	${SODIUM_SOURCE_DIR}/crypto_sign/ed25519/sign_ed25519.c
	${SODIUM_SOURCE_DIR}/crypto_stream/chacha20/dolbeau/chacha20_dolbeau-avx2.c
	${SODIUM_SOURCE_DIR}/crypto_stream/chacha20/dolbeau/chacha20_dolbeau-ssse3.c
	${SODIUM_SOURCE_DIR}/crypto_stream/chacha20/ref/chacha20_ref.c
	${SODIUM_SOURCE_DIR}/crypto_stream/chacha20/stream_chacha20.c
	${SODIUM_SOURCE_DIR}/crypto_stream/crypto_stream.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa20/ref/salsa20_ref.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa20/stream_salsa20.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa20/xmm6/salsa20_xmm6.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa20/xmm6int/salsa20_xmm6int-avx2.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa20/xmm6int/salsa20_xmm6int-sse2.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa2012/ref/stream_salsa2012_ref.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa2012/stream_salsa2012.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa208/ref/stream_salsa208_ref.c
	${SODIUM_SOURCE_DIR}/crypto_stream/salsa208/stream_salsa208.c
	${SODIUM_SOURCE_DIR}/crypto_stream/xchacha20/stream_xchacha20.c
	${SODIUM_SOURCE_DIR}/crypto_stream/xsalsa20/stream_xsalsa20.c
	${SODIUM_SOURCE_DIR}/crypto_verify/sodium/verify.c
	${SODIUM_SOURCE_DIR}/randombytes/internal/randombytes_internal_random.c
	${SODIUM_SOURCE_DIR}/randombytes/randombytes.c
	${SODIUM_SOURCE_DIR}/randombytes/sysrandom/randombytes_sysrandom.c
	${SODIUM_SOURCE_DIR}/sodium/codecs.c
	${SODIUM_SOURCE_DIR}/sodium/core.c
	${SODIUM_SOURCE_DIR}/sodium/runtime.c
	${SODIUM_SOURCE_DIR}/sodium/utils.c
	${SODIUM_SOURCE_DIR}/sodium/version.c
)

set(LIB_VERSION 23)

option(SODIUM_BUILD_SHARED "Whether or not to build the shared object" ON)
option(SODIUM_BUILD_STATIC "Whether or not to build the static archive" ON)

if (NOT SODIUM_BUILD_SHARED AND NOT SODIUM_BUILD_STATIC)
  message(FATAL_ERROR "Neither static nor shared library build enabled")
endif()

IF (NOT MSVC)
 	# avoid building everything twice for shared + static
  	# only on *nix, as Windows needs different preprocessor defines in static builds
  	add_library (sodium_objects OBJECT ${sodium_sources})
  	set_property(TARGET sodium_objects PROPERTY POSITION_INDEPENDENT_CODE ON)
  	target_include_directories(sodium_objects
    	PUBLIC
    		"$<BUILD_INTERFACE:${PUBLIC_INCLUDE_DIR}>"
      		"$<INSTALL_INTERFACE:${PUBLIC_INCLUDE_DIR_FOR_INSTALL}>"
    	PRIVATE
    		"${PRIVATE_INCLUDE_DIR}"
    )
ENDIF(NOT MSVC)

if (SODIUM_BUILD_SHARED)
	IF (APPLE)
    	add_library(sodium SHARED ${sodium_sources})
    ELSE (APPLE)
    	IF (MSVC)
      		add_library(sodium SHARED ${sodium_sources})
    	ELSE (MSVC)
      		add_library(sodium SHARED $<TARGET_OBJECTS:sodium_objects>)
    	ENDIF (MSVC)
    ENDIF(APPLE)

	set_target_properties (sodium PROPERTIES
	    PUBLIC_HEADER "${public_headers}"
	    DEFINE_SYMBOL "SODIUM_EXPORTS"
	    VERSION "${LIB_VERSION}"
	    if(APPLE)
	    	 MACHO_COMPATIBILITY_VERSION 27
	    	 MACHO_CURRENT_VERSION 27
	   	ENDIF(APPLE)
	    COMPILE_DEFINITIONS "DLL_EXPORT"
	    OUTPUT_NAME "sodium"
	    PREFIX "lib"
	  )

	install(TARGETS sodium
		EXPORT sodium-targets
    	LIBRARY DESTINATION "lib${LIB_SUFFIX}" # .so file
    	ARCHIVE DESTINATION "lib${LIB_SUFFIX}" # .lib file
    	RUNTIME DESTINATION bin                # .dll file
  	)

	target_include_directories(sodium
	    PUBLIC
    		"$<BUILD_INTERFACE:${PUBLIC_INCLUDE_DIR}>"
      		"$<INSTALL_INTERFACE:${PUBLIC_INCLUDE_DIR_FOR_INSTALL}>"
    	PRIVATE
    		"${PRIVATE_INCLUDE_DIR}"
	  )
endif()

if (SODIUM_BUILD_STATIC)
	IF (APPLE)
    	add_library(sodium-static STATIC ${sodium_sources})
    ELSE (APPLE)
    	IF (MSVC)
      		add_library(sodium-static STATIC ${sodium_sources})
    	ELSE (MSVC)
      		add_library(sodium-static STATIC $<TARGET_OBJECTS:sodium_objects>)
    	ENDIF (MSVC)
    ENDIF(APPLE)

	set_target_properties(sodium-static PROPERTIES
	    PUBLIC_HEADER "${public_headers}"
	    COMPILE_DEFINITIONS "SODIUM_STATIC"
	    OUTPUT_NAME "sodium"
	    PREFIX "lib"
	  )
	install(TARGETS sodium-static
		EXPORT sodium-targets
    	LIBRARY DESTINATION "lib${LIB_SUFFIX}" # .so file
    	ARCHIVE DESTINATION "lib${LIB_SUFFIX}" # .lib file
    	RUNTIME DESTINATION bin                # .dll file
  	)
  	target_include_directories(sodium-static
    	PUBLIC
    		"$<BUILD_INTERFACE:${PUBLIC_INCLUDE_DIR}>"
      		"$<INSTALL_INTERFACE:${PUBLIC_INCLUDE_DIR_FOR_INSTALL}>"
    	PRIVATE
    		"${PRIVATE_INCLUDE_DIR}"
  	)
  	target_compile_definitions(sodium-static
    	PUBLIC SODIUM_STATIC
  	)
endif()

########################################################################
# pkgconfig
########################################################################

configure_file(
    "${SODIUM_SOURCE_DIR}/include/sodium/version.h.in"
    "${SODIUM_SOURCE_DIR}/include/sodium/version.h")

########################################################################
# installer
########################################################################

include(CMakePackageConfigHelpers)
if (WIN32)
  set(SODIUM_CMAKECONFIG_INSTALL_DIR "CMake" CACHE STRING "install path for sodiumConfig.cmake")
else()
  # GNUInstallDirs "DATADIR" wrong here; CMake search path wants "share".
  set(SODIUM_CMAKECONFIG_INSTALL_DIR "share/cmake/sodium" CACHE STRING "install path for sodiumConfig.cmake")
endif()

if (NOT CMAKE_VERSION VERSION_LESS 3.0)
  export(EXPORT sodium-targets
         FILE "${CMAKE_CURRENT_BINARY_DIR}/sodiumTargets.cmake")
endif()
configure_package_config_file(builds/cmake/Config.cmake.in
                              "${CMAKE_CURRENT_BINARY_DIR}/sodiumConfig.cmake"
                              INSTALL_DESTINATION ${SODIUM_CMAKECONFIG_INSTALL_DIR})
write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/sodiumConfigVersion.cmake
                                 VERSION ${VERSION}
                                 COMPATIBILITY AnyNewerVersion)
install(EXPORT sodium-targets
        FILE sodiumTargets.cmake
        DESTINATION ${SODIUM_CMAKECONFIG_INSTALL_DIR})
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/sodiumConfig.cmake
              ${CMAKE_CURRENT_BINARY_DIR}/sodiumConfigVersion.cmake
              DESTINATION ${SODIUM_CMAKECONFIG_INSTALL_DIR})
