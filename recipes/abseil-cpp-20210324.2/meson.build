project('abseil-cpp', 'cpp',
  version : '20210324.2',
  license : 'Apache-2.0',
  default_options : [
    'cpp_std=c++11',
  ])

cpp = meson.get_compiler('cpp')

cpp_flags = [
  '-Wno-sign-compare'
]

if host_machine.cpu_family() == 'x86_64'
  cpp_flags += [ '-maes', '-msse4.1' ]
elif host_machine.cpu_family() == 'aarch64' and cpp.sizeof('void*') == 8
  cpp_flags += [ '-march=armv8-a+crypto' ]
elif host_machine.cpu_family() == 'arm' and cpp.sizeof('void*') == 4
  cpp_flags += [ '-mfpu=neon' ]
endif

flags = cpp.get_supported_arguments(cpp_flags)

if cpp.get_id() == 'msvc'
  flags += ['/DNOMINMAX'] # Don't define min and max macros (windows.h)
endif

add_project_arguments(flags, language: 'cpp')

libatomic = dependency('', required: false)
if cpp.get_id() == 'msvc'
    # VS does not not need args for atomic ops.
elif not cpp.links('''#include <stdint.h>
                      int main() {
                        struct {
                          uint64_t *v;
                        } x;
                        return (int)__atomic_load_n(x.v, __ATOMIC_ACQUIRE) &
                               (int)__atomic_add_fetch(x.v, (uint64_t)1, __ATOMIC_ACQ_REL);
                      }''',
                name : 'GCC atomic builtins')
  libatomic = cpp.find_library('atomic')
endif

absl_include_dir = include_directories('.')

# Group files by the containing library
absl_base_sources = files(
  'absl/base/internal/cycleclock.cc',
  'absl/base/internal/exponential_biased.cc',
  'absl/base/internal/low_level_alloc.cc',
  'absl/base/internal/periodic_sampler.cc',
  'absl/base/internal/raw_logging.cc',
  'absl/base/internal/scoped_set_env.cc',
  'absl/base/internal/spinlock_wait.cc',
  'absl/base/internal/spinlock.cc',
  'absl/base/internal/sysinfo.cc',
  'absl/base/internal/strerror.cc',
  'absl/base/internal/thread_identity.cc',
  'absl/base/internal/throw_delegate.cc',
  'absl/base/internal/unscaledcycleclock.cc',
  'absl/base/log_severity.cc',
)

absl_container_sources = files(
  'absl/container/internal/hashtablez_sampler_force_weak_definition.cc',
  'absl/container/internal/hashtablez_sampler.cc',
  'absl/container/internal/raw_hash_set.cc',
)

absl_debugging_sources = files(
  'absl/debugging/failure_signal_handler.cc',
  'absl/debugging/internal/address_is_readable.cc',
  'absl/debugging/internal/demangle.cc',
  'absl/debugging/internal/elf_mem_image.cc',
  'absl/debugging/internal/examine_stack.cc',
  'absl/debugging/internal/stack_consumption.cc',
  'absl/debugging/internal/vdso_support.cc',
  'absl/debugging/leak_check_disable.cc',
  'absl/debugging/leak_check.cc',
  'absl/debugging/stacktrace.cc',
  'absl/debugging/symbolize.cc',
)

absl_flags_sources = files(
  'absl/flags/commandlineflag.cc',
  'absl/flags/flag.cc',
  'absl/flags/internal/commandlineflag.cc',
  'absl/flags/internal/flag.cc',
  'absl/flags/internal/program_name.cc',
  'absl/flags/internal/private_handle_accessor.cc',
  'absl/flags/internal/usage.cc',
  'absl/flags/marshalling.cc',
  'absl/flags/parse.cc',
  'absl/flags/reflection.cc',
  'absl/flags/usage_config.cc',
  'absl/flags/usage.cc',
)

absl_hash_sources = files(
  'absl/hash/internal/city.cc',
  'absl/hash/internal/hash.cc',
  'absl/hash/internal/wyhash.cc',
)

absl_numeric_sources = files(
  'absl/numeric/int128.cc',
)

absl_random_sources = files(
  'absl/random/discrete_distribution.cc',
  'absl/random/gaussian_distribution.cc',
  'absl/random/internal/chi_square.cc',
  'absl/random/internal/distribution_test_util.cc',
  'absl/random/internal/pool_urbg.cc',
  'absl/random/internal/randen_detect.cc',
  'absl/random/internal/randen_hwaes.cc',
  'absl/random/internal/randen_slow.cc',
  'absl/random/internal/randen_round_keys.cc',
  'absl/random/internal/randen.cc',
  'absl/random/internal/seed_material.cc',
  'absl/random/seed_gen_exception.cc',
  'absl/random/seed_sequences.cc',
)

absl_status_sources = files(
  'absl/status/status_payload_printer.cc',
  'absl/status/status.cc',
  'absl/status/statusor.cc',
)

absl_strings_sources = files(
  'absl/strings/ascii.cc',
  'absl/strings/charconv.cc',
  'absl/strings/cord.cc',
  'absl/strings/escaping.cc',
  'absl/strings/internal/charconv_bigint.cc',
  'absl/strings/internal/charconv_parse.cc',
  'absl/strings/internal/cord_internal.cc',
  'absl/strings/internal/cord_rep_ring.cc',
  'absl/strings/internal/escaping.cc',
  'absl/strings/internal/memutil.cc',
  'absl/strings/internal/ostringstream.cc',
  'absl/strings/internal/pow10_helper.cc',
  'absl/strings/internal/str_format/arg.cc',
  'absl/strings/internal/str_format/bind.cc',
  'absl/strings/internal/str_format/extension.cc',
  'absl/strings/internal/str_format/float_conversion.cc',
  'absl/strings/internal/str_format/output.cc',
  'absl/strings/internal/str_format/parser.cc',
  'absl/strings/internal/utf8.cc',
  'absl/strings/match.cc',
  'absl/strings/numbers.cc',
  'absl/strings/str_cat.cc',
  'absl/strings/str_replace.cc',
  'absl/strings/str_split.cc',
  'absl/strings/string_view.cc',
  'absl/strings/substitute.cc',
)

absl_synchronization_sources = files(
  'absl/synchronization/barrier.cc',
  'absl/synchronization/blocking_counter.cc',
  'absl/synchronization/internal/create_thread_identity.cc',
  'absl/synchronization/internal/graphcycles.cc',
  'absl/synchronization/internal/per_thread_sem.cc',
  'absl/synchronization/internal/waiter.cc',
  'absl/synchronization/mutex.cc',
  'absl/synchronization/notification.cc',
)

absl_time_sources = files(
  'absl/time/civil_time.cc',
  'absl/time/clock.cc',
  'absl/time/duration.cc',
  'absl/time/format.cc',
  'absl/time/internal/cctz/src/civil_time_detail.cc',
  'absl/time/internal/cctz/src/time_zone_fixed.cc',
  'absl/time/internal/cctz/src/time_zone_format.cc',
  'absl/time/internal/cctz/src/time_zone_if.cc',
  'absl/time/internal/cctz/src/time_zone_impl.cc',
  'absl/time/internal/cctz/src/time_zone_info.cc',
  'absl/time/internal/cctz/src/time_zone_libc.cc',
  'absl/time/internal/cctz/src/time_zone_lookup.cc',
  'absl/time/internal/cctz/src/time_zone_posix.cc',
  'absl/time/internal/cctz/src/zone_info_source.cc',
  'absl/time/time.cc',
)

absl_types_sources = files(
  'absl/types/bad_any_cast.cc',
  'absl/types/bad_optional_access.cc',
  'absl/types/bad_variant_access.cc',
)

# Libraries
absl_base_lib = static_library(
  'absl_base',
  absl_base_sources,
  include_directories: absl_include_dir,
  dependencies: [dependency('threads'), libatomic],
  install : true,
)

absl_hash_lib = static_library(
  'absl_hash',
  absl_hash_sources,
  include_directories: absl_include_dir,
  install : true,
)

absl_numeric_lib = static_library(
  'absl_numeric',
  absl_numeric_sources,
  include_directories: absl_include_dir,
#  install : true,
)

absl_strings_lib = static_library(
  'absl_strings',
  absl_strings_sources,
  include_directories: absl_include_dir,
  link_with: [
    absl_base_lib,
    absl_numeric_lib,
  ],
  install : true,
)

absl_debugging_lib = static_library(
  'absl_debugging',
  absl_debugging_sources,
  include_directories: absl_include_dir,
  link_with: [
    absl_base_lib,
    absl_strings_lib,
  ],
  dependencies: libatomic,
#  install : true,
)

absl_random_lib = static_library(
  'absl_random',
  absl_random_sources,
  include_directories: absl_include_dir,
  link_with: [
    absl_base_lib,
    absl_strings_lib,
  ],
  dependencies: libatomic,
#  install : true,
)

absl_time_lib = static_library(
  'absl_time',
  absl_time_sources,
  include_directories: absl_include_dir,
  link_with: [
    absl_base_lib,
    absl_numeric_lib,
    absl_strings_lib,
  ],
  # macOS only, upstream: https://github.com/abseil/abseil-cpp/pull/280
  dependencies : [dependency('CoreFoundation', required : false)],
  install : true,
)

absl_types_lib = static_library(
  'absl_types',
  absl_types_sources,
  include_directories: absl_include_dir,
  install : true,
)

absl_synchronization_lib = static_library(
  'absl_synchronization',
  absl_synchronization_sources,
  include_directories: absl_include_dir,
  link_with: [
    absl_base_lib,
    absl_debugging_lib,
    absl_time_lib,
  ],
  install : true,
)

absl_container_lib = static_library(
  'absl_container',
  absl_container_sources,
  include_directories: absl_include_dir,
  link_with: [
    absl_base_lib,
    absl_debugging_lib,
    absl_hash_lib,
    absl_synchronization_lib,
    absl_time_lib,
  ],
#  install : true,
)

#absl_flags_lib = static_library(
#  'absl_flags',
#  absl_flags_sources,
#  include_directories: absl_include_dir,
#  link_with: [
#    absl_base_lib,
#    absl_container_lib,
#    absl_hash_lib,
#    absl_strings_lib,
#    absl_synchronization_lib,
#  ],
#  dependencies: libatomic,
#  install : true,
#)

absl_status_lib = static_library(
  'absl_status',
  absl_status_sources,
  include_directories: absl_include_dir,
  link_with: [
    absl_base_lib,
    absl_strings_lib,
  ],
#  install : true,
)

# Dependencies
absl_base_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_base_lib,
)

absl_hash_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_hash_lib,
)

absl_numeric_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_numeric_lib,
)

absl_strings_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_strings_lib,
  dependencies: [
    absl_base_dep,
    absl_numeric_dep,
  ],
)

absl_debugging_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_debugging_lib,
  dependencies: [
    absl_base_dep,
    absl_strings_dep,
  ],
)

absl_random_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_random_lib,
  dependencies: [
    absl_base_dep,
    absl_strings_dep,
  ],
)

absl_time_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_time_lib,
  dependencies: [
    absl_base_dep,
    absl_numeric_dep,
    absl_strings_dep,
  ],
)

absl_types_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_types_lib,
)

absl_synchronization_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_synchronization_lib,
  dependencies: [
    absl_base_dep,
    absl_debugging_dep,
    absl_time_dep,
  ],
)

absl_container_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_container_lib,
  dependencies: [
    absl_base_dep,
    absl_debugging_dep,
    absl_hash_dep,
    absl_synchronization_dep,
    absl_time_dep,
  ],
)

#absl_flags_dep = declare_dependency(
#  include_directories: absl_include_dir,
#  link_with: absl_flags_lib,
#  dependencies: [
#    absl_base_dep,
#    absl_container_dep,
#    absl_hash_dep,
#    absl_strings_dep,
#    absl_synchronization_dep,
#  ],
#)

absl_status_dep = declare_dependency(
  include_directories: absl_include_dir,
  link_with: absl_status_lib,
  dependencies: [
    absl_base_dep,
    absl_strings_dep,
  ],
)

##########################################################
#   define headers to install_headers to public
##########################################################

absl_base_headers = files(
  'absl/base/log_severity.h',
  'absl/base/attributes.h',
  'absl/base/config.h',
  'absl/base/const_init.h',
  'absl/base/macros.h',
  'absl/base/optimization.h',
  'absl/base/options.h',
  'absl/base/policy_checks.h',
  'absl/base/port.h',
)
install_headers(absl_base_headers, subdir:'absl/base/')

absl_base_headers_internal = files(
  'absl/base/internal/cycleclock.h',
  'absl/base/internal/exponential_biased.h',
  'absl/base/internal/low_level_alloc.h',
  'absl/base/internal/periodic_sampler.h',
  'absl/base/internal/raw_logging.h',
  'absl/base/internal/scoped_set_env.h',
  'absl/base/internal/spinlock_wait.h',
  'absl/base/internal/spinlock.h',
  'absl/base/internal/sysinfo.h',
  'absl/base/internal/strerror.h',
  'absl/base/internal/thread_identity.h',
  'absl/base/internal/throw_delegate.h',
  'absl/base/internal/unscaledcycleclock.h',
  'absl/base/internal/identity.h',
  'absl/base/internal/inline_variable.h',
  'absl/base/internal/invoke.h',
)
install_headers(absl_base_headers_internal, subdir:'absl/base/internal/')

absl_container_headers_internal = files(
#  'absl/container/internal/hashtablez_sampler_force_weak_definition.cc',
  'absl/container/internal/hashtablez_sampler.h',
  'absl/container/internal/raw_hash_set.h',
)
install_headers(absl_container_headers_internal, subdir:'absl/container/internal/')

absl_debugging_headers = files(
  'absl/debugging/failure_signal_handler.h',
#  'absl/debugging/leak_check_disable.cc',
  'absl/debugging/leak_check.h',
  'absl/debugging/stacktrace.h',
  'absl/debugging/symbolize.h',
)
install_headers(absl_debugging_headers, subdir:'absl/debugging/')

absl_debugging_headers_internal = files(
  'absl/debugging/internal/address_is_readable.h',
  'absl/debugging/internal/demangle.h',
  'absl/debugging/internal/elf_mem_image.h',
  'absl/debugging/internal/examine_stack.h',
  'absl/debugging/internal/stack_consumption.h',
  'absl/debugging/internal/vdso_support.h',
)
install_headers(absl_debugging_headers_internal, subdir:'absl/debugging/internal/')

absl_flags_headers = files(
  'absl/flags/commandlineflag.h',
  'absl/flags/flag.h',
  'absl/flags/marshalling.h',
  'absl/flags/parse.h',
  'absl/flags/reflection.h',
  'absl/flags/usage_config.h',
  'absl/flags/usage.h',
)
#install_headers(absl_flags_headers, subdir:'absl/flags/')

absl_flags_headers_internal = files(
  'absl/flags/internal/commandlineflag.h',
  'absl/flags/internal/flag.h',
  'absl/flags/internal/program_name.h',
  'absl/flags/internal/private_handle_accessor.h',
  'absl/flags/internal/usage.h',
)
#install_headers(absl_flags_headers_internal, subdir:'absl/flags/internal/')

absl_hash_headers_internal = files(
  'absl/hash/internal/city.h',
  'absl/hash/internal/hash.h',
  'absl/hash/internal/wyhash.h',
)
install_headers(absl_hash_headers_internal, subdir:'absl/hash/internal/')

absl_numeric_headers = files(
  'absl/numeric/int128.h',
)
install_headers(absl_numeric_headers, subdir:'absl/numeric/')

absl_random_headers = files(
  'absl/random/discrete_distribution.h',
  'absl/random/gaussian_distribution.h',
  'absl/random/seed_gen_exception.h',
  'absl/random/seed_sequences.h',
)
install_headers(absl_random_headers, subdir:'absl/random/')
absl_random_headers_internal = files(
  'absl/random/internal/chi_square.h',
  'absl/random/internal/distribution_test_util.h',
  'absl/random/internal/pool_urbg.h',
  'absl/random/internal/randen_detect.h',
  'absl/random/internal/randen_hwaes.h',
  'absl/random/internal/randen_slow.h',
#  'absl/random/internal/randen_round_keys.cc',
  'absl/random/internal/randen.h',
  'absl/random/internal/seed_material.h',
)
install_headers(absl_random_headers_internal, subdir:'absl/random/internal/')

absl_status_headers = files(
  'absl/status/status_payload_printer.h',
  'absl/status/status.h',
  'absl/status/statusor.h',
)
install_headers(absl_status_headers, subdir:'absl/status/')

absl_strings_headers = files(
  'absl/strings/ascii.h',
  'absl/strings/charconv.h',
  'absl/strings/cord.h',
  'absl/strings/escaping.h',
  'absl/strings/match.h',
  'absl/strings/numbers.h',
  'absl/strings/str_cat.h',
  'absl/strings/str_replace.h',
  'absl/strings/str_split.h',
  'absl/strings/string_view.h',
  'absl/strings/substitute.h',
)
install_headers(absl_strings_headers, subdir:'absl/strings/')

absl_strings_headers_internal = files(
  'absl/strings/internal/charconv_bigint.h',
  'absl/strings/internal/charconv_parse.h',
  'absl/strings/internal/cord_internal.h',
  'absl/strings/internal/cord_rep_ring.h',
  'absl/strings/internal/escaping.h',
  'absl/strings/internal/memutil.h',
  'absl/strings/internal/ostringstream.h',
  'absl/strings/internal/pow10_helper.h',
  'absl/strings/internal/utf8.h',
)
install_headers(absl_strings_headers_internal, subdir:'absl/strings/internal/')

absl_strings_headers_internal_str_format = files(
  'absl/strings/internal/str_format/arg.h',
  'absl/strings/internal/str_format/bind.h',
  'absl/strings/internal/str_format/extension.h',
  'absl/strings/internal/str_format/float_conversion.h',
  'absl/strings/internal/str_format/output.h',
  'absl/strings/internal/str_format/parser.h',
)
install_headers(absl_strings_headers_internal_str_format, subdir:'absl/strings/internal/str_format/')

absl_synchronization_headers = files(
  'absl/synchronization/barrier.h',
  'absl/synchronization/blocking_counter.h',
  'absl/synchronization/mutex.h',
  'absl/synchronization/notification.h',
)
install_headers(absl_synchronization_headers, subdir:'absl/synchronization/')

absl_synchronization_headers_internal = files(
  'absl/synchronization/internal/create_thread_identity.h',
  'absl/synchronization/internal/graphcycles.h',
  'absl/synchronization/internal/per_thread_sem.h',
  'absl/synchronization/internal/waiter.h',
)
install_headers(absl_strings_headers_internal_str_format, subdir:'absl/synchronization/internal/')

absl_time_headers = files(
  'absl/time/civil_time.h',
  'absl/time/clock.h',
#  'absl/time/duration.cc',
#  'absl/time/format.cc',
  'absl/time/time.h',
)

install_headers(absl_time_headers, subdir:'absl/time/')

absl_time_headers_internal_cctz_src = files(
#  'absl/time/internal/cctz/src/civil_time_detail.cc',
  'absl/time/internal/cctz/src/time_zone_fixed.h',
#  'absl/time/internal/cctz/src/time_zone_format.cc',
  'absl/time/internal/cctz/src/time_zone_if.h',
  'absl/time/internal/cctz/src/time_zone_impl.h',
  'absl/time/internal/cctz/src/time_zone_info.h',
  'absl/time/internal/cctz/src/time_zone_libc.h',
#  'absl/time/internal/cctz/src/time_zone_lookup.cc',
  'absl/time/internal/cctz/src/time_zone_posix.h',
#  'absl/time/internal/cctz/src/zone_info_source.cc',
)
install_headers(absl_time_headers_internal_cctz_src, subdir:'absl/time/internal/cctz/src/')

absl_time_headers_internal_cctz_include = files(
  'absl/time/internal/cctz/include/cctz/civil_time_detail.h',
  'absl/time/internal/cctz/include/cctz/zone_info_source.h',
)
install_headers(absl_time_headers_internal_cctz_include, subdir:'absl/time/internal/cctz/include/')

absl_types_headers = files(
  'absl/types/bad_any_cast.h',
  'absl/types/bad_optional_access.h',
  'absl/types/bad_variant_access.h',
  'absl/types/optional.h',
)
install_headers(absl_types_headers, subdir:'absl/types/')

absl_types_headers_internal = files(
  'absl/types/internal/optional.h',
)
install_headers(absl_types_headers_internal, subdir:'absl/types/internal/')

absl_memory_headers = files(
  'absl/memory/memory.h',
)
install_headers(absl_memory_headers, subdir:'absl/memory/')

absl_meta_headers = files(
  'absl/meta/type_traits.h',
)
install_headers(absl_meta_headers, subdir:'absl/meta/')

absl_utility_headers = files(
  'absl/utility/utility.h',
)
install_headers(absl_utility_headers, subdir:'absl/utility/')

##########################################################
#   To build 1 single absl shared_library
##########################################################

#absl_sources = [absl_base_sources,
#    absl_hash_sources,
#    absl_numeric_sources,
#    absl_strings_sources,
#    absl_debugging_sources,
#    absl_random_sources,
#    absl_time_sources,
#    absl_types_sources,
#    absl_synchronization_sources,
#    absl_container_sources,
#    absl_flags_sources,
#    absl_status_sources]

#absl_lib = shared_library('absl',
#  absl_sources,
#  include_directories : absl_include_dir,
#  c_args : cpp_flags,
#  link_args : ['-pthread'],
#  install : true,
#  version : meson.project_version(),
#)

##########################################################
#   install *.pc
##########################################################
pkg = import('pkgconfig')
#pkg.generate(absl_lib,
#  name : 'libabsl',
#  description : 'Abseil Common Libraries (C++)',
#  version :  meson.project_version(),
#)

pkg.generate(absl_base_lib,
  name : 'libabsl_base',
  description : 'Abseil Common Libraries (C++)',
  version :  meson.project_version(),
)

pkg.generate(absl_hash_lib,
  name : 'libabsl_hash',
  description : 'Abseil Common Libraries (C++)',
  version :  meson.project_version(),
)

#pkg.generate(absl_numeric_lib,
#  name : 'libabsl_numeric',
#  description : 'Abseil Common Libraries (C++)',
#  version :  meson.project_version(),
#)

pkg.generate(absl_strings_lib,
  name : 'libabsl_strings',
  description : 'Abseil Common Libraries (C++)',
  version :  meson.project_version(),
)

#pkg.generate(absl_debugging_lib,
#  name : 'libabsl_debugging',
#  description : 'Abseil Common Libraries (C++)',
#  version :  meson.project_version(),
#)

#pkg.generate(absl_random_lib,
#  name : 'libabsl_random',
#  description : 'Abseil Common Libraries (C++)',
#  version :  meson.project_version(),
#)

pkg.generate(absl_time_lib,
  name : 'libabsl_time',
  description : 'Abseil Common Libraries (C++)',
  version :  meson.project_version(),
)

pkg.generate(absl_types_lib,
  name : 'libabsl_types',
  description : 'Abseil Common Libraries (C++)',
  version :  meson.project_version(),
)

pkg.generate(absl_synchronization_lib,
  name : 'libabsl_synchronization',
  description : 'Abseil Common Libraries (C++)',
  version :  meson.project_version(),
)

#pkg.generate(absl_container_lib,
#  name : 'libabsl_container',
#  description : 'Abseil Common Libraries (C++)',
#  version :  meson.project_version(),
#)

#pkg.generate(absl_flags_lib,
#  name : 'libabsl_flags',
#  description : 'Abseil Common Libraries (C++)',
#  version :  meson.project_version(),
#)

#pkg.generate(absl_status_lib,
#  name : 'libabsl_status',
#  description : 'Abseil Common Libraries (C++)',
#  version :  meson.project_version(),
#)

##########################################################
#   comment
##########################################################

# meson build --prefix=/home/syuu/Downloads/abseil-cpp-20210324.2/myout
# ninja -C build
# ninja install -C build

