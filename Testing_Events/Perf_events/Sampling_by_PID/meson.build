project('CPUConsumptionModule', version: '0.3.1')
add_languages('c', 'cpp')

clang = find_program('clang')
bpftool = find_program('bpftool')
libbpf_dep = dependency('libbpf', required: true)


projectinc = [include_directories('.', 'include')]

project_deps = [libbpf_dep]



subdir('src')
