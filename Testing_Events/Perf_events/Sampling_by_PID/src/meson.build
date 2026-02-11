bpf_o = 'prog.bpf.o'
bpf_skel_h = 'prog.skel.h'

# Compile eBPF program
bpf_prog = custom_target('bpf_prog',
	command: [clang, '-O2', '-g', '-target', 'bpf', '-D__TARGET_ARCH_x86',
		'-I' + meson.current_source_dir() / '..' / 'include',
		'-c', '@INPUT@', '-o', '@OUTPUT@'],
	input: 'prog.bpf.c',
	output: bpf_o,
	depends: vmlinux
)

# Generate skeleton from eBPF object
skeleton = custom_target('bpf_skeleton',
	command: ['bpftool', 'gen', 'skeleton', '@INPUT@'],
	input: bpf_prog,
	output: bpf_skel_h,
	capture: true,
	build_by_default: true
)

# Create a dependency on the skeleton generation
skeleton_dep = declare_dependency(
	include_directories: include_directories('.'),
	sources: skeleton
)

# Sampler executable
sampler_exe = executable('sampler',
	'main.cpp',
	include_directories: [projectinc, include_directories('.' / '..' / 'include')],
	dependencies: [project_deps, skeleton_dep],
	cpp_args: ['-O0', '-g', '-Wall'],
	install: true
)

# Benchmark executable
benchmark_exe = executable('bench',
	'benchmark.c',
	c_args: ['-O0', '-I/usr/include/openblas'],
	link_args: ['-lopenblas', '-lm'],
	install: true
)
