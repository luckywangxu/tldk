libraries = [
        'tle_dring', 
        'tle_memtank',
        'tle_misc',
        'tle_timer',
        'tle_l4p'
]

dpdk = dependency('libdpdk')
set_variable('static_tle_dpdk', dpdk)

static_libraries = []

foreach l:libraries
    sources = []
    headers = []
    includes = []
    objs = []
    deps = []
    static_deps = []
    cflags = []
    cflags += '-Wfatal-errors'
    name = l
    libdir = 'lib' + name
    subdir(libdir)

    foreach d:deps
        if not is_variable('static_tle_' + d)
            error('Missing internal dependency "@0@" for @1@ [@2@]'
                    .format(d, name, 'lib/' + l))
        endif
        static_deps += [get_variable('static_tle_' + d)]
    endforeach

    includes += include_directories(libdir)
    static_lib = static_library(name,
            sources,
            objects: objs,
            c_args: cflags,
            dependencies: static_deps,
            include_directories: includes,
            install: true)
    static_dep = declare_dependency(
            include_directories: includes,
            dependencies: static_deps)
    set_variable('static_tle_' + name, static_dep)
    install_headers(headers)
    
    static_libraries += [static_lib] + static_libraries
endforeach

pkg = import('pkgconfig')
pkg.generate(
        name: 'libtldk',
        description: 'Transport Layer Development Kit (TLDK)',
        version: meson.project_version(),
        libraries_private: static_libraries,
        requires_private: []
)
