# Quantization and hls4ml 

Quantization is the process of reducing the numerical bit precision of your neural network's weights, biases, and activation functions. By converting standard 32-bit floating-point networks (`float32`) into custom narrow fixed-point configurations (e.g., 6-bit or 8-bit integers), you can drastically reduce FPGA resource usage (LUTs and DSPs) and clock latency.

KalEdge uses **QKeras** for Quantization-Aware Training (QAT) and **hls4ml** for translating those quantized graphs directly into high-speed C++ firmware.

---

## Best Practices for QKeras Layer Design

To ensure your QKeras models compile successfully into hardware without throwing converter errors, follow these strict design rules:

### 1. Separate Activations from Layers 
In standard Keras, you can define activations inline: `Dense(32, activation='relu')`.  
In QKeras, **do not define quantized activations inline**. Always declare activations as a separate `QActivation` layer.


*   
    ```python
    qkeras.QDense(32, kernel_quantizer="quantized_bits(8,0)", bias_quantizer="quantized_bits(8,0)"),
    qkeras.QActivation("quantized_relu(8,0)")
    ```

### 2. Handle final `softmax` with standard Keras 
`softmax` is a non-linear exponential-based activation used to compute class probabilities. It is not quantized. 
Passing `'softmax'` to `QActivation` or using it inside a quantized layer can cause hls4ml converter exceptions (`Unsupported QKeras activation`).
QActivation("softmax")
    
*   
    ```python
    qkeras.QDense(num_classes, kernel_quantizer="quantized_bits(8,0)"),
    tf.keras.layers.Activation("softmax")
    ```

### 3. Avoid `alpha=1` parameter in Quantizers
While QKeras allows the scaling parameter `alpha=1` inside `quantized_bits`, hls4ml parser structures are optimized for standard bit/integer parameters. Omit `alpha` to prevent compatibility hiccups.

*   **Avoid:** `quantized_bits(6, 0, alpha=1)`
*   **Use:** `quantized_bits(6, 0)`

---

## Recommended Bit Precision (Fixed-Point Format)

In fixed-point math, numbers are represented as `ap_fixed<W, I>`, where:
*   **`W` (Width):** Total number of bits allocated to the number.
*   **`I` (Integer):** Number of bits allocated to the integer part (before the decimal point).
*   **`W - I`:** Bits left for the fractional part (precision).

### Typical Bit Configurations:
*   **8-bit Quantization (High Accuracy):** `quantized_bits(8, 0)` for weights and `quantized_relu(8, 0)` for activations. Re-scales inputs to `ap_fixed<8, 1>`. Excellent balance; usually retains >98% of baseline float accuracy.
*   **6-bit Quantization (High Compression):** `quantized_bits(6, 0)`. Drastically reduces LUT and DSP occupancy. Perfect for fitting larger networks onto small boards like the Xilinx Pynq-Z2.
*   **Binary / Ternary (Ultra-TinyML):** QKeras supports binary (`binary()`) and ternary (`ternary()`) quantization. This replaces multipliers with simple adders/subtractors in hardware, saving massive DSP blocks.

---

## hls4ml Conversion Settings

When you transition your model in the **hls4ml** tab:
1.  **Reuse Factor (RF):** Controls hardware parallelization. 
    *   `RF = 1`: Fully parallelized (lowest latency, highest resource usage).
    *   `RF > 1`: Sequential reuse of physical multiplier units (saves resources, increases latency).
2.  **Strategy:** 
    *   `Latency`: Optimizes for lowest latency (ideal for RF=1).
    *   `Resource`: Optimizes for smallest resource footprint (ideal for RF > 1).
3.  **Simulation Verification:** Always click **Run Simulation** after converting. This runs a cycle-accurate C-simulation of the model with your test dataset, letting you verify that the hardware implementation matches Keras' precision before writing a single line of VHDL/Verilog.
