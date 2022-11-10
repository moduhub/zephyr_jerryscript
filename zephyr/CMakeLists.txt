
if(CONFIG_JERRYSCRIPT)

set(ZEPHYR_CURRENT_LIBRARY jerryscript)
include_directories(${ZEPHYR_BASE}/lib/jerryscript)

zephyr_interface_library_named(JERRYSCRIPT)

set(JERRY_CMDLINE_TEST          OFF     CACHE   BOOL    "Build jerry test command line tool?")
set(JERRY_CMDLINE               OFF     CACHE   BOOL    "Build jerry command line tool?")
set(JERRY_CMDLINE_SNAPSHOT      OFF     CACHE   BOOL    "Build jerry snapshot command line tool?")
set(JERRY_LIBFUZZER             OFF     CACHE   BOOL    "Build jerry with libfuzzer support?")
set(JERRY_PORT                  ON      CACHE   BOOL    "Build default jerry port implementation?")
set(JERRY_EXT                   ON      CACHE   BOOL    "Build jerry-ext?")
set(JERRY_MATH                  ON      CACHE   BOOL    "Build and use jerry-math?")
set(JERRY_PARSER                ON      CACHE   BOOL    "Use parser?")
set(JERRY_GLOBAL_HEAP_SIZE      "(70)"  CACHE   STRING  "Size of memory heap, in kilobytes")
set(JERRY_SNAPSHOT_EXEC         ON      CACHE   BOOL    "Execute Jerry Snapshot?")
set(UNITTESTS                   OFF     CACHE   BOOL    "Build unit tests?")
set(DOCTESTS                    OFF     CACHE   BOOL    "Build doc tests?")
set(BUILD_SHARED_LIBS           OFF     CACHE   BOOL    "Build shared libraries?")
set(ENABLE_AMALGAM              OFF     CACHE   BOOL    "Enable amalgamated build?")
set(ENABLE_LTO                  OFF     CACHE   BOOL    "Enable LTO build?")
set(ENABLE_STRIP                ON      CACHE   BOOL    "Enable stripping all symbols from release binary?")
set(ENABLE_COMPILE_COMMANDS     ON      CACHE   BOOL    "Enable generating compile_commands.json?")

zephyr_library()

# Jerry Core
zephyr_include_directories(
    ../jerry-core/
    ../jerry-core/api
    ../jerry-core/debugger
    ../jerry-core/ecma/base
    ../jerry-core/ecma/builtin-objects
    ../jerry-core/ecma/builtin-objects/typedarray
    ../jerry-core/ecma/operations
    ../jerry-core/include
    ../jerry-core/jcontext
    ../jerry-core/jmem
    ../jerry-core/jrt
    ../jerry-core/lit
    ../jerry-core/parser/js
    ../jerry-core/parser/regexp
    ../jerry-core/profiles
    ../jerry-core/vm
)

zephyr_library_sources(
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

# ECMA Builtin
zephyr_library_sources(
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

# Jerry Port
if(NOT CONFIG_JERRY_PORT)
    message(STATUS "Jerry Port Disabled")
    else()
    message(STATUS "Jerry Port Enabled")
endif()

zephyr_library_sources_ifdef(CONFIG_JERRY_PORT 
    ../jerry-port/common/jerry-port-context.c
    ../jerry-port/common/jerry-port-fs.c
    ../jerry-port/common/jerry-port-io.c
    ../jerry-port/common/jerry-port-process.c

    ../jerry-port/zephyr/jerry-port.c
    ../jerry-port/zephyr/getline-zephyr.c
    ../jerry-port/zephyr/getline-zephyr.c
)

# Jerry Math
if(NOT CONFIG_JERRY_MATH)
    message(STATUS "Jerry Math Disabled")
    else()
    message(STATUS "Jerry Math Enabled")
endif()

# MUST FIX THE MAX .h INCLUDE
zephyr_include_directories(../jerry-math ../jerry-math/include)

zephyr_library_sources_ifdef(CONFIG_JERRY_MATH
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
zephyr_include_directories(
    ../jerry-ext/include
    ../jerry-ext/common
)

zephyr_library_sources(
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

zephyr_library_link_libraries(JERRYSCRIPT)
target_link_libraries(JERRYSCRIPT INTERFACE zephyr_interface)

endif()
