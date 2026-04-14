# EfiMon.CPUConsumptionModule
Sistema de monitoreo eficiente de consumo de CPU en Linux, utilizando tecnología eBPF para obtener trazabilidad de bajo impacto y en tiempo real del comportamiento del sistema operativo.
## Construcción del Módulo eBPF

Este proyecto utiliza **Meson** como sistema de compilación. A continuación se detallan los pasos para construir el módulo eBPF.

### Requisitos Previos

Asegúrate de tener instaladas las siguientes dependencias:

```bash
sudo apt-get update
sudo apt-get install -y \
    build-essential \
    clang \
    llvm \
    libelf-dev \
    libz-dev \
    pkg-config \
    libbpf-dev \
    bpftool \
    meson \
    ninja-build \
    python3 \
    openblas-dev
```

### Pasos de Compilación

1. **Navega al directorio del módulo**:
```bash
cd EfiMon.CPUConsumptionModule/Testing_Events/Perf_events/Sampling_by_PID
```

2. **Configura el entorno de compilación** (solo la primera vez o después de cambios importantes):
```bash
meson setup builddir
```

Para limpiar y reconfigura desde cero:
```bash
meson setup --wipe builddir
```

3. **Compila el proyecto**:
```bash
meson compile -C builddir
```

### Instalación

Para instalar los binarios compilados en el sistema:

```bash
sudo meson install -C builddir
```

### Compilación Detallada del Proceso

El sistema de compilación realiza los siguientes pasos automáticamente:

1. **Genera vmlinux.h**: Extrae las definiciones de tipos del kernel desde BTF (BPF Type Format)
```bash
bpftool btf dump file /sys/kernel/btf/vmlinux format c > vmlinux.h
```

2. **Compila el programa eBPF** con clang:
```bash
clang -O2 -g -target bpf -D__TARGET_ARCH_x86 -c src/prog.bpf.c -o prog.bpf.o
```

3. **Genera el skeleton** del programa eBPF:
```bash
bpftool gen skeleton prog.bpf.o > prog.skel.h
```

4. **Compila el programa en espacio de usuario**:
   - **sampler.cpp**: Aplicación principal que interactúa con el kernel y el eBPF
   - **benchmark.c**: Herramienta de benchmark para pruebas de rendimiento

### Archivos de Configuración de Compilación

- **meson.build**: Configuración principal del proyecto
- **src/meson.build**: Definición de targets de compilación (sampler, bench, etc.)
- **include/meson.build**: Generación de headers de eBPF

### Solución de Problemas Comunes

**Error: "bpftool not found"**
```bash
sudo apt-get install linux-tools-generic
```

**Error: "libbpf not found"**
```bash
sudo apt-get install libbpf-dev
```

**Error: "clang not found"**
```bash
sudo apt-get install clang llvm
```

**Limpiar compilación anterior**:
```bash
rm -rf builddir
meson setup builddir
meson compile -C builddir
```
