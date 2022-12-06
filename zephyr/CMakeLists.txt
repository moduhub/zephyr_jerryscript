
if(CONFIG_JERRYSCRIPT)

set(ZEPHYR_CURRENT_LIBRARY jerryscript)
include_directories(${ZEPHYR_BASE}/lib/jerryscript)

zephyr_interface_library_named(JERRYSCRIPT)

zephyr_library()

# These values must be used to build with zephyr
set(JERRY_CMDLINE_TEST OFF)
set(JERRY_CMDLINE OFF)

#KConfig helper macros
macro(jerry_add_bool_from_kconfig JERRY_NAME)
    if(${ARGC} EQUAL 2)
        message(STATUS "jerry_add_bool_from_kconfig ${JERRY_NAME}=${ARGV1}")
        if(${ARGV1})
            set(DEFINES_JERRY ${DEFINES_JERRY} ${JERRY_NAME}=1)
        else()
            set(DEFINES_JERRY ${DEFINES_JERRY} ${JERRY_NAME}=0)
        endif()
    else()
        message(STATUS "KCONFIG option for ${JERRY_NAME} undefined")
    endif()
endmacro(jerry_add_bool_from_kconfig)

macro(jerry_add_string_from_kconfig JERRY_NAME KCONFIG_VALUE)
    set(DEFINES_JERRY ${DEFINES_JERRY} ${JERRY_NAME}=${KCONFIG_VALUE})
endmacro(jerry_add_string_from_kconfig)

# Helper macro to set 0/1 switch as Jerry Defines
macro(jerry_define NAME VALUE)
  if(${VALUE})
    set(DEFINES_JERRY ${DEFINES_JERRY} ${NAME}=1)
  else()
    set(DEFINES_JERRY ${DEFINES_JERRY} ${NAME}=0)
  endif()
endmacro(jerry_define)

#Routines to import configs from Profile folder
# if (CONFIG_JERRY_PROFILE_MINIMAL)
# set(JERRY_PROFILE "${CMAKE_CURRENT_SOURCE_DIR}/../jerry-core/profiles/minimal.profile")
# endif()

# if (CONFIG_JERRY_PROFILE_ESNEXT)
# set(JERRY_PROFILE "${CMAKE_CURRENT_SOURCE_DIR}/../jerry-core/profiles/es.next.profile")
# endif()

# if(EXISTS ${JERRY_PROFILE})
# message("Using Jerry Profile: ${JERRY_PROFILE}")
# file(READ "${JERRY_PROFILE}" PROFILE_SETTINGS)
# string(REGEX REPLACE "[ \t]*#[^\n]*" "" PROFILE_SETTINGS "${PROFILE_SETTINGS}")
# string(REGEX REPLACE "[\r\n]" ";" PROFILE_SETTINGS "${PROFILE_SETTINGS}")

# # Process entries and save them as CMake variables.
# # This is required to correctly generate the jerryscript-config.h file.
# foreach(PROFILE_ENTRY ${PROFILE_SETTINGS})
# string(REPLACE "=" ";" PROFILE_ENTRY "${PROFILE_ENTRY}")
# list(GET PROFILE_ENTRY 0 PROFILE_KEY)
# list(GET PROFILE_ENTRY 1 PROFILE_VALUE)
# set(${PROFILE_KEY} ${PROFILE_VALUE})
# endforeach()

# set(DEFINES_JERRY ${DEFINES_JERRY} ${PROFILE_SETTINGS})

# message("Profile options added: ${PROFILE_SETTINGS}")
# else()
# message(FATAL_ERROR "Profile file: '${JERRY_PROFILE}' doesn't exist!")
# endif()

#Memory management
jerry_add_string_from_kconfig(JERRY_GLOBAL_HEAP_SIZE    ${CONFIG_JERRY_GLOBAL_HEAP_SIZE})
jerry_add_string_from_kconfig(JERRY_GC_LIMIT            ${CONFIG_JERRY_GC_LIMIT})
jerry_add_string_from_kconfig(JERRY_STACK_LIMIT         ${CONFIG_JERRY_STACK_LIMIT})
jerry_add_string_from_kconfig(JERRY_GC_MARK_LIMIT       ${CONFIG_JERRY_GC_MARK_LIMIT})

# Jerry options
# build mode specific compile/link flags
#set(DEFINES_JERRY ${DEFINES_JERRY} $<$<NOT:$<CONFIG:Debug>>:JERRY_NDEBUG>)

# FIXED DEFINES enforced by Zephyr requirements
jerry_define(JERRY_ERROR_MESSAGES               y)
jerry_define(JERRY_EXTERNAL_CONTEXT             n)
jerry_define(JERRY_FUNCTION_TO_STRING           n)
jerry_define(JERRY_LINE_INFO                    n)
jerry_define(JERRY_LOGGING                      y)
jerry_define(JERRY_MEM_STATS                    n)
jerry_define(JERRY_DEBUGGER                     n)
jerry_define(JERRY_MEM_GC_BEFORE_EACH_ALLOC     n)
jerry_define(JERRY_PARSER_DUMP_BYTE_CODE        n)
jerry_define(JERRY_PROMISE_CALLBACK             n)
jerry_define(JERRY_REGEXP_STRICT_MODE           n)
jerry_define(JERRY_REGEXP_DUMP_BYTE_CODE        n)
jerry_define(JERRY_SYSTEM_ALLOCATOR             n)
jerry_define(JERRY_VM_HALT                      n)
jerry_define(JERRY_VM_THROW                     n)

#User KCONFIG'ed defines
jerry_add_bool_from_kconfig(JERRY_PARSER            ${CONFIG_JERRY_PARSER})
jerry_add_bool_from_kconfig(JERRY_SNAPSHOT_EXEC     ${CONFIG_JERRY_SNAPSHOT_EXEC})
jerry_add_bool_from_kconfig(JERRY_SNAPSHOT_SAVE     ${CONFIG_JERRY_SNAPSHOT_SAVE})
jerry_add_bool_from_kconfig(JERRY_CPOINTER_32_BIT   ${CONFIG_JERRY_CPOINTER_32_BIT})

#Builtins
if(CONFIG_JERRY_BUILTINS)
jerry_define(JERRY_BUILTINS               y)
jerry_add_bool_from_kconfig(JERRY_BUILTIN_ANNEXB    ${CONFIG_JERRY_BUILTIN_ANNEXB})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_ARRAY     ${CONFIG_JERRY_BUILTIN_ARRAY})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_BOOLEAN   ${CONFIG_JERRY_BUILTIN_BOOLEAN})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_DATE      ${CONFIG_JERRY_BUILTIN_DATE})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_ERRORS    ${CONFIG_JERRY_BUILTIN_ERRORS})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_JSON      ${CONFIG_JERRY_BUILTIN_JSON})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_MATH      ${CONFIG_JERRY_BUILTIN_MATH})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_NUMBER    ${CONFIG_JERRY_BUILTIN_NUMBER})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_REGEXP    ${CONFIG_JERRY_BUILTIN_REGEXP})
jerry_add_bool_from_kconfig(JERRY_BUILTIN_STRING    ${CONFIG_JERRY_BUILTIN_STRING})
else()
jerry_define(JERRY_BUILTINS               n)
message("All JerryScript builtins disabled (minimal profile)")
endif()

set(JERRY_CORE_INTERFACE_INCLUDES
    ../jerry-core/include)

set(JERRY_CORE_PRIVATE_INCLUDES
    ../jerry-core/
    ../jerry-core/api
    ../jerry-core/debugger
    ../jerry-core/ecma/base
    ../jerry-core/ecma/builtin-objects
    ../jerry-core/ecma/builtin-objects/typedarray
    ../jerry-core/ecma/operations
    ../jerry-core/jcontext
    ../jerry-core/jmem
    ../jerry-core/jrt
    ../jerry-core/lit
    ../jerry-core/parser/js
    ../jerry-core/parser/regexp
    ../jerry-core/profiles
    ../jerry-core/vm)

set (JERRY_CORE_SOURCES
    ../jerry-core/api/jerry-debugger-transport.c
    ../jerry-core/api/jerry-debugger.c
    ../jerry-core/api/jerry-module.c
    ../jerry-core/api/jerry-snapshot.c
    ../jerry-core/api/jerryscript.c
    ../jerry-core/debugger/debugger.c
    ../jerry-core/ecma/base/ecma-alloc.c
    ../jerry-core/ecma/base/ecma-gc.c
    ../jerry-core/ecma/base/ecma-errors.c
    ../jerry-core/ecma/base/ecma-extended-info.c
    ../jerry-core/ecma/base/ecma-helpers-collection.c
    ../jerry-core/ecma/base/ecma-helpers-conversion.c
    ../jerry-core/ecma/base/ecma-helpers-errol.c
    ../jerry-core/ecma/base/ecma-helpers-external-pointers.c
    ../jerry-core/ecma/base/ecma-helpers-number.c
    ../jerry-core/ecma/base/ecma-helpers-string.c
    ../jerry-core/ecma/base/ecma-helpers-value.c
    ../jerry-core/ecma/base/ecma-helpers.c
    ../jerry-core/ecma/base/ecma-init-finalize.c
    ../jerry-core/ecma/base/ecma-lcache.c
    ../jerry-core/ecma/base/ecma-line-info.c
    ../jerry-core/ecma/base/ecma-literal-storage.c
    ../jerry-core/ecma/base/ecma-module.c
    ../jerry-core/ecma/base/ecma-property-hashmap.c
    ../jerry-core/ecma/operations/ecma-arguments-object.c
    ../jerry-core/ecma/operations/ecma-array-object.c
    ../jerry-core/ecma/operations/ecma-arraybuffer-object.c
    ../jerry-core/ecma/operations/ecma-async-generator-object.c
    ../jerry-core/ecma/operations/ecma-atomics-object.c
    ../jerry-core/ecma/operations/ecma-big-uint.c
    ../jerry-core/ecma/operations/ecma-bigint-object.c
    ../jerry-core/ecma/operations/ecma-bigint.c
    ../jerry-core/ecma/operations/ecma-boolean-object.c
    ../jerry-core/ecma/operations/ecma-comparison.c
    ../jerry-core/ecma/operations/ecma-container-object.c
    ../jerry-core/ecma/operations/ecma-conversion.c
    ../jerry-core/ecma/operations/ecma-dataview-object.c
    ../jerry-core/ecma/operations/ecma-eval.c
    ../jerry-core/ecma/operations/ecma-exceptions.c
    ../jerry-core/ecma/operations/ecma-function-object.c
    ../jerry-core/ecma/operations/ecma-get-put-value.c
    ../jerry-core/ecma/operations/ecma-iterator-object.c
    ../jerry-core/ecma/operations/ecma-jobqueue.c
    ../jerry-core/ecma/operations/ecma-lex-env.c
    ../jerry-core/ecma/operations/ecma-number-object.c
    ../jerry-core/ecma/operations/ecma-objects-general.c
    ../jerry-core/ecma/operations/ecma-objects.c
    ../jerry-core/ecma/operations/ecma-promise-object.c
    ../jerry-core/ecma/operations/ecma-proxy-object.c
    ../jerry-core/ecma/operations/ecma-reference.c
    ../jerry-core/ecma/operations/ecma-regexp-object.c
    ../jerry-core/ecma/operations/ecma-shared-arraybuffer-object.c
    ../jerry-core/ecma/operations/ecma-string-object.c
    ../jerry-core/ecma/operations/ecma-symbol-object.c
    ../jerry-core/ecma/operations/ecma-typedarray-object.c
    ../jerry-core/jcontext/jcontext.c
    ../jerry-core/jmem/jmem-allocator.c
    ../jerry-core/jmem/jmem-heap.c
    ../jerry-core/jmem/jmem-poolman.c
    ../jerry-core/jrt/jrt-fatals.c
    ../jerry-core/jrt/jrt-logging.c
    ../jerry-core/lit/lit-char-helpers.c
    ../jerry-core/lit/lit-magic-strings.c
    ../jerry-core/lit/lit-strings.c
    ../jerry-core/parser/js/byte-code.c
    ../jerry-core/parser/js/common.c
    ../jerry-core/parser/js/js-lexer.c
    ../jerry-core/parser/js/js-parser-expr.c
    ../jerry-core/parser/js/js-parser-line-info-create.c
    ../jerry-core/parser/js/js-parser-mem.c
    ../jerry-core/parser/js/js-parser-module.c
    ../jerry-core/parser/js/js-parser-statm.c
    ../jerry-core/parser/js/js-parser-tagged-template-literal.c
    ../jerry-core/parser/js/js-parser-util.c
    ../jerry-core/parser/js/js-parser.c
    ../jerry-core/parser/js/js-scanner-ops.c
    ../jerry-core/parser/js/js-scanner-util.c
    ../jerry-core/parser/js/js-scanner.c
    ../jerry-core/parser/js/parser-errors.c
    ../jerry-core/parser/regexp/re-bytecode.c
    ../jerry-core/parser/regexp/re-compiler.c
    ../jerry-core/parser/regexp/re-parser.c
    ../jerry-core/vm/opcodes-ecma-arithmetics.c
    ../jerry-core/vm/opcodes-ecma-bitwise.c
    ../jerry-core/vm/opcodes-ecma-relational-equality.c
    ../jerry-core/vm/opcodes.c
    ../jerry-core/vm/vm-stack.c
    ../jerry-core/vm/vm-utils.c
    ../jerry-core/vm/vm.c
)

set(JERRY_ECMA_BUILTINS_SOURCES
    ../jerry-core/ecma/builtin-objects/ecma-builtin-aggregateerror.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-aggregateerror-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-array-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-array-prototype-unscopables.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-array-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-array.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-arraybuffer-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-arraybuffer.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-async-from-sync-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-async-function-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-async-function.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-async-generator-function.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-async-generator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-async-generator.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-async-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-atomics.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-bigint-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-bigint.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-boolean-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-boolean.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-dataview-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-dataview.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-date-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-date.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-error-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-error.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-evalerror-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-evalerror.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-function-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-function.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-generator-function.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-generator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-generator.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-global.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-handlers.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-helpers-date.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-helpers-error.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-helpers-json.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-helpers-sort.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-helpers.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-intrinsic.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-json.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-map-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-map-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-map.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-math.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-number-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-number.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-object-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-object.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-promise-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-promise.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-proxy.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-rangeerror-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-rangeerror.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-referenceerror-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-referenceerror.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-reflect.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-regexp-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-regexp-string-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-regexp.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-set-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-set-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-set.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-shared-arraybuffer-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-shared-arraybuffer.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-string-iterator-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-string-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-string.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-symbol-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-symbol.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-syntaxerror-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-syntaxerror.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-type-error-thrower.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-typeerror-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-typeerror.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-urierror-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-urierror.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-weakmap-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-weakmap.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-weakref-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-weakref.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-weakset-prototype.c
    ../jerry-core/ecma/builtin-objects/ecma-builtin-weakset.c
    ../jerry-core/ecma/builtin-objects/ecma-builtins.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-bigint64array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-bigint64array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-biguint64array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-biguint64array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-float32array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-float32array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-float64array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-float64array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-int16array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-int16array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-int32array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-int32array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-int8array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-int8array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-typedarray-helpers.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-typedarray-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-typedarray.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint16array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint16array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint32array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint32array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint8array-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint8array.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint8clampedarray-prototype.c
    ../jerry-core/ecma/builtin-objects/typedarray/ecma-builtin-uint8clampedarray.c
)

set(JERRY_PORT_SOURCES
    ../jerry-port/common/jerry-port-context.c
    ../jerry-port/common/jerry-port-fs.c
    ../jerry-port/common/jerry-port-io.c
    ../jerry-port/common/jerry-port-process.c
    ../jerry-port/zephyr/jerry-port.c
    ../jerry-port/zephyr/getline-zephyr.c
    ../jerry-port/zephyr/getline-zephyr.c
)

set(JERRY_MATH_INCLUDES,
    ../jerry-math
    ../jerry-math/include
)

set(JERRY_MATH_SOURCES
    ../jerry-math/acos.c
    ../jerry-math/acosh.c
    ../jerry-math/asin.c
    ../jerry-math/asinh.c
    ../jerry-math/atan.c
    ../jerry-math/atan2.c
    ../jerry-math/atanh.c
    ../jerry-math/cbrt.c
    ../jerry-math/ceil.c
    ../jerry-math/copysign.c
    ../jerry-math/cosh.c
    ../jerry-math/exp.c
    ../jerry-math/expm1.c
    ../jerry-math/fabs.c
    ../jerry-math/floor.c
    ../jerry-math/fmod.c
    ../jerry-math/log.c
    ../jerry-math/log10.c
    ../jerry-math/log1p.c
    ../jerry-math/log2.c
    ../jerry-math/nextafter.c
    ../jerry-math/pow.c
    ../jerry-math/scalbn.c
    ../jerry-math/sinh.c
    ../jerry-math/sqrt.c
    ../jerry-math/tanh.c
    ../jerry-math/trig.c
)

# Jerry Ext
set(JERRY_EXT_INCLUDES
    ../jerry-ext/include
    ../jerry-ext/common
)

set(JERRY_EXT_SOURCES
    ../jerry-ext/arg/arg-js-iterator-helper.c
    ../jerry-ext/arg/arg-transform-functions.c
    ../jerry-ext/arg/arg.c
    ../jerry-ext/debugger/debugger-common.c
    ../jerry-ext/debugger/debugger-rp.c
    ../jerry-ext/debugger/debugger-serial.c
    ../jerry-ext/debugger/debugger-sha1.c
    ../jerry-ext/debugger/debugger-tcp.c
    ../jerry-ext/debugger/debugger-ws.c
    ../jerry-ext/handle-scope/handle-scope-allocator.c
    ../jerry-ext/handle-scope/handle-scope.c
    ../jerry-ext/module/module.c
    ../jerry-ext/util/handlers.c
    ../jerry-ext/util/print.c
    ../jerry-ext/util/properties.c
    ../jerry-ext/util/repl.c
    ../jerry-ext/util/sources.c
    ../jerry-ext/util/test262.c
)

zephyr_compile_definitions(${DEFINES_JERRY})

message(STATUS "DEFINES_JERRY: ${DEFINES_JERRY}")

#Core
zephyr_library_include_directories(${JERRY_CORE_PRIVATE_INCLUDES})
zephyr_include_directories(${JERRY_CORE_INTERFACE_INCLUDES})
zephyr_library_sources(${JERRY_CORE_SOURCES})

#Port
zephyr_library_sources(${JERRY_PORT_SOURCES})

#Ext
zephyr_library_include_directories(${JERRY_EXT_INCLUDES})
zephyr_include_directories(${JERRY_EXT_INCLUDES})
zephyr_library_sources(${JERRY_EXT_SOURCES})

#if(CONFIG_JERRY_BUILTINS)
zephyr_library_sources(${JERRY_ECMA_BUILTINS_SOURCES})
#endif()

if(CONFIG_JERRY_MATH)
    zephyr_library_sources(${JERRY_MATH_SOURCES})
    zephyr_include_directories(${JERRY_MATH_INCLUDES})
endif()

#if(CONFIG_JERRY_EXT)
#    zephyr_library_sources(${JERRY_EXT_SOURCES})
#    zephyr_include_directories(${JERRY_EXT_INCLUDES})
#endif()

zephyr_library_link_libraries(JERRYSCRIPT)
target_link_libraries(JERRYSCRIPT INTERFACE zephyr_interface)

endif()
