# TurboQuant Build and Usage Guide

This repository provides instructions for building and running llama.cpp with specific optimizations for AMD GPUs using ROCm.

## 🚀 Overview

This guide details the process of building `llama.cpp` to leverage AMD's ROCm stack for GPU acceleration, specifically enabling advanced features like ROCmWMMA Flash Attention.

## ⚙️ Installation & Build

Follow these steps to clone the repository and build the optimized binaries.

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/ggml-org/llama.cpp
   cd llama.cpp
   ```

2. **Build with ROCm/HIP Support:**
   Run the following command in the `llama.cpp` directory. This command uses `cmake` to configure the build for AMD ROCm and then builds the project.

   ```bash
   HIPCXX="$(hipconfig -l)/clang" HIP_PATH="$(hipconfig -R)" \
   cmake -S . -B build -DGGML_HIP=ON -DCMAKE_BUILD_TYPE=Release -DGGML_HIP_ROCWMMA_FATTN=ON \
   && cmake --build build --config Release -- -j 16
   ```

### Technical Details:

*   **ROCm Integration:** This command builds `llama.cpp` to use AMD's ROCm stack for GPU acceleration. The ROCm library provides CUDA-compatible APIs for AMD GPUs, allowing `llama.cpp` to run efficiently on Radeon hardware.
*   **ROCmWMMA Flash Attention:** The `-DGGML_HIP_ROCWMMA_FATTN=ON` flag enables a specialized implementation of flash attention using AMD's `rocWMMA` library. This provides:
    *   **Better memory efficiency:** Attention operations process data in smaller chunks, reducing memory bandwidth usage.
    *   **Higher throughput:** `rocWMMA` is optimized specifically for AMD GPU architectures.
    *   **Reduced memory access:** Attention patterns are more cache-friendly.
*   **Build Configuration:** The `Release` build type enables compiler optimizations (`-O3`, `-ffast-math`, etc.) that significantly improve inference performance compared to Debug builds.

### System Requirements:

| Requirement | Details |
| :--- | :--- |
| **GPU** | AMD GPU with ROCm support (Radeon RX 5000/6000/7000 series, MI series) |
| **ROCm** | ROCm SDK installed and configured |
| **hipconfig** | `hipconfig` tool available in PATH |
| **ROCmWMMA** | `rocWMMA` headers must be present (included with ROCm SDK by default) |

## 🚀 Inference

Once the build is complete, you can run the server using the optimized binary:

```bash
/path/to/llama.cpp-turboquant/build/bin/llama-server \
-m "$MODEL_PATH" \
-ngl "$GPU_LAYERS" \
-c "$CONTEXT_LENGTH" \
-n "$MAX_TOKENS" \
-b "$BATCH_SIZE" \
-t "$NUM_THREADS" \
-ctk "turbo3" \
-ctv "turbo3" \
--host "$HOST" \
--port "$PORT"
```

**Note:** The arguments `-ctk "turbo3"` and `-ctv "turbo3"` enable TurboQuant features. You can also use `"turbo4"`.

---
*Source Documentation:* [https://github.com/domvox/llama.cpp-turboquant-hip/blob/main/docs/build.md](https://github.com/domvox/llama.cpp-turboquant-hip/blob/main/docs/build.md)
_Note: The specific build process is executed by the provided build script in the `turboQuant` directory._