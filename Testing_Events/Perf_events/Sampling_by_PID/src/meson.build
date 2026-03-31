bpf_o = 'prog.bpf.o'
bpf_skel_h = 'prog.skel.h'

# Generate vmlinux.h first
vmlinux = custom_target('vmlinux',
  command: [
    'sh', '-c',
    'bpftool btf dump file /sys/kernel/btf/vmlinux format c > ../src/vmlinux.h'
  ],
	output: 'vmlinux.h',
)


# Compile eBPF program
bpf_prog = custom_target('bpf_prog',
	input: 'prog.bpf.c',
	output: bpf_o,
	command: [
	clang,
	'-target', 'bpf',
	'-D__TARGET_ARCH_x86',
	'-O2', '-g',
		'-I', meson.current_source_dir(),
		'-I', meson.current_build_dir() / 'src',
		'-c', '@INPUT@',
		'-o', '@OUTPUT@'],
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
