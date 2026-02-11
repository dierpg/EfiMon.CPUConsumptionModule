project('CPUConsumptionModule', version: '0.3.1')
add_languages('c', 'cpp')

clang = find_program('clang')
bpftool = find_program('bpftool')
libbpf_dep = dependency('libbpf', required: true)

projectinc = [include_directories('.', 'include')]

project_deps = [libbpf_dep]

# Generate vmlinux.h first
vmfile = 'vmlinux.h'
vmlinux = custom_target('vmlinux',
	command: ['bpftool', 'btf', 'dump', 'file', '/sys/kernel/btf/vmlinux', 'format', 'c'],
	output: vmfile,
	capture: true,
	build_by_default: true
)

subdir('src')
