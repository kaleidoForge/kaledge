# Getting Started with KalEdge

Welcome to **KalEdge**! This guide will take you step-by-step from creating an account to training, optimizing, and preparing your first deep learning model for FPGA deployment.

---

## 1. Create an Account and Select your Plan
1.  Navigate to [kaledge.kaleidoforge.com](https://kaledge.kaleidoforge.com) and click **Sign Up**.
2.  Select a subscription tier that fits your needs:
    *   **Hobbyist:** Great for learning and testing basic models with native datasets.
    *   **Supporter:** Perfect for personal projects. Includes custom CSV uploads, PDF analytics reports, TF-MOT pruning, and the conversational **AI Architect** (BYOK).
    *   **Developer:** Ideal for universities and training. Adds Knowledge Distillation (KD), and QKeras quantization.
    *   **Pro:** Complete end-to-end suite. Unlocks the automatic FPGA Estimator, remote Xilinx Build Agent synthesis (under development), IP Wrapper generation, and full priority build pipelines.

---

## 2. Load and Analyze your Dataset
Once logged in, navigate to the **Dataset** tab:
1.  **Select Data Source:** Choose a native dataset (MNIST, CIFAR-10) or upload your own **Custom CSV** file.
2.  **Dataset Layout:** Ensure your custom CSV is formatted correctly (see the [Dataset Format Guide](dataset-format.md)).
3.  **Use the AI Dataset Agent (Upcoming):** Once released (currently under active development for Pro accounts), clicking the AI analysis button will activate our local agent to analyze your feature distributions, check for imbalances, and recommend the best normalization strategy.
4.  **Confirm Split:** Set your preferred training, validation, and test split ratios, then click **Load Dataset**.

---

## 3. Design your Architecture
Navigate to the **Architecture** tab:
1.  **Define your layers:** Write your sequential Keras model in Python syntax or select a pre-built template.
2.  **Use AI Architect (Supporter & Above):** 
    *   If you have a Supporter plan or higher, make sure to paste your **Anthropic API Key** in the **AI Configuration** panel in the sidebar.
    *   Open the **AI Architect** section, describe the network you want to build in plain English (e.g., *"A dense neural network with two hidden layers for 10-class MNIST digit classification"*), and click **Generate**.
    *   The agent will generate a floating-point **Baseline** model, a compressed **Student** model, and a quantized **QKeras** model.
    *   Review the code and the beautiful interactive SVG layer charts, then click **Apply** to load the selected model directly into your active workspace.

---

## 4. Train and Establish your Reference
Navigate to the **Training** (Baseline) tab:
1.  **Set Hyperparameters:** Configure the learning rate, batch size, training epochs, and optimizer.
2.  **Enable K-Fold Cross-Validation (Optional):** Toggle **Enable K-Fold Cross Validation** and select your desired number of folds ($k=2$ to $k=10$, default is 5) to run shuffled cross-validation. This is highly recommended to guarantee model stability.
3.  **Train the Model:** Click **Start Training**. Watch the training metrics (Loss and Accuracy) plot in real-time.
4.  **Evaluate:** Upon completion, review the confusion matrix, classification reports, and test metrics. This floating-point model serves as your accuracy "ceiling".

---

# 5. Optimize and Compress (Knowledge Distillation, Pruning, & Quantization)
To fit your model onto an FPGA and make it extremely fast, you must structurally and numerically compress its weight footprint:
*   **Knowledge Distillation (Developer/Pro):** Navigate to the **Knowledge Distillation** tab to train your lightweight "Student" model, forcing it to mimic the outputs of your high-accuracy "Teacher" baseline.
*   **Pruning (All Tiers):** Run structured and unstructured weight pruning via TF-MOT to drive redundant connection weights to zero, dramatically reducing hardware resource footprint while preserving accuracy.
*   **QKeras Quantization & QAP (Developer/Pro):** Navigate to the **QKeras** tab to replace standard floating-point layers with arbitrary-precision fixed-point layers (e.g., 6-bit or 8-bit quantization) and train using QAT (Quantization Aware Training). For maximum hardware efficiency, you can also run joint **Quantization-Aware Pruning (QAP)** to prune and quantize weights simultaneously within the same training loop.
*   **Preset Optimization Pipelines:** Execute pre-established optimization sequences (such as *KD -> Pruning -> QAT* or *SVD -> QAP*) using the **Pipeline** orchestrator. This allows you to evaluate and select the best overall model configuration based on your priorities (Accuracy vs. File Size vs. FPGA resources).
*   **Multi-Run with Random Seeds (Statistical Reliability):** To guarantee that your compression metrics are robust and not subject to seed bias, enable the **Multi-Run (random seeds)** selector. This runs each step in the pipeline $N$ times with different seeds and displays a per-seed breakdown alongside mean metrics and standard deviations ($\pm \text{std}$).

---

## 6. Estimate, Simulate, and Synthesize (hls4ml & FPGA Firmware)
Once your network is fully compressed, you can convert it to hardware description language and plan your physical deployment:
1.  **Estimate Resources:** Use the **Surrogate Resource Estimator** (currently in Beta and under testing) to predict the hardware occupancy (LUT, FF, DSP, BRAM) of your quantized model on target FPGA boards before launching synthesis.
2.  **HLS Conversion & Simulation:** Convert your QKeras model to C++ using **hls4ml** in the **hls4ml** tab. Test and verify bit-accurate simulation outputs against your Python results.
3.  **Export HLS Project:** Download your complete synthesizable C++ Vivado HLS project as a `.zip` archive. During export, you can choose whether to download a raw design or include an integrated **AXI4-Stream / DMA (Direct Memory Access)** IP core wrapper for seamless SoC integration. For Pro accounts, automatic bitstream synthesis via the local **Build Agent** is currently under active development and will be accessible via the **FPGA Firmware** tab.

Now you are ready! Proceed to the [Dataset Format Guide](dataset-format.md) to prepare your data.
