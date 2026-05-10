# Getting Started with KalEdge

Welcome to **KalEdge**! This guide will take you step-by-step from creating an account to training, optimizing, and preparing your first deep learning model for FPGA deployment.

---

## 1. Create an Account and Select your Plan
1.  Navigate to [kaledge.kaleidoforge.com](https://kaledge.kaleidoforge.com) and click **Sign Up**.
2.  Select a subscription tier that fits your needs:
    *   **Hobbyist (Free):** Great for learning and testing basic models with native datasets.
    *   **Supporter (€12/mo):** Perfect for personal projects. Includes custom CSV uploads, PDF analytics reports, TF-MOT pruning, and the conversational **AI Architect** (BYOK).
    *   **Developer (€49/mo):** Ideal for universities and training. Adds Knowledge Distillation (KD), and QKeras quantization.
    *   **Pro (€149/mo):** Complete end-to-end suite. Unlocks the automatic FPGA Estimator, remote Xilinx Build Agent synthesis (under development), IP Wrapper generation, and full priority build pipelines.

---

## 2. Load and Analyze your Dataset
Once logged in, navigate to the **Dataset** tab:
1.  **Select Data Source:** Choose a native dataset (MNIST, CIFAR-10) or upload your own **Custom CSV** file.
2.  **Dataset Layout:** Ensure your custom CSV is formatted correctly (see the [Dataset Format Guide](dataset-format.md)).
3.  **Use the AI Dataset Agent:** Click the AI analysis button to let our local agent analyze your feature distributions, check for imbalances, and recommend the best normalization strategy (e.g., MinMax or Standard scaling).
4.  **Confirm Split:** Set your preferred training, validation, and test split ratios, then click **Load Dataset**.

---

## 3. Design your Architecture
Navigate to the **Architecture** tab:
1.  **Define your layers:** Write your sequential Keras model in Python syntax or select a pre-built template.
2.  **Use AI Architect (Supporter & Above):** 
    *   If you have a Supporter plan or higher, make sure to paste your **Anthropic API Key** in the **AI Configuration** panel in the sidebar.
    *   Open the **AI Architect** section, describe the network you want to build in plain English (e.g., *"A 1D convolutional neural network for 16-channel spectrum classification with 3 classes"*), and click **Generate**.
    *   The agent will generate a floating-point **Baseline** model, a compressed **Student** model, and a quantized **QKeras** model.
    *   Review the code and the beautiful interactive SVG layer charts, then click **Apply** to load the selected model directly into your active workspace.

---

## 4. Train and Establish your Reference
Navigate to the **Training** (Baseline) tab:
1.  **Set Hyperparameters:** Configure the learning rate, batch size, training epochs, and optimizer.
2.  **Train the Model:** Click **Start Training**. Watch the training metrics (Loss and Accuracy) plot in real-time.
3.  **Evaluate:** Upon completion, review the confusion matrix, classification reports, and test metrics. This floating-point model serves as your accuracy "ceiling" (reference reference).

---

## 5. Optimize and Compress (Knowledge Distillation & Joint Pipelines)
To fit your model onto an FPGA, you must minimize its size and parameter footprint:
*   **Knowledge Distillation (Developer/Pro):** Navigate to the **Knowledge Distillation** tab to train your lightweight "Student" model, forcing it to mimic the outputs of your high-accuracy "Teacher" baseline.
*   **Pruning & QAP (All Tiers):** Run weight pruning via TF-MOT to drive redundant connection weights to zero. You can also run **Quantization-Aware Pruning (QAP)** to jointly prune and quantize weights simultaneously, achieving maximum hardware efficiency.
*   **Preset Optimization Pipelines:** Execute pre-established optimization sequences (such as *KD -> Pruning -> QAT* or *SVD -> QAP*) using the **Pipeline** orchestrator. This allows you to evaluate and select the best overall model configuration based on your priorities (Accuracy vs. File Size vs. FPGA resources).

---

## 6. Quantize and Synthesize (QKeras & hls4ml)
Now that your network is structurally compressed, you must reduce its numerical bitwidth:
1.  **Quantize (QKeras):** Navigate to the **QKeras** tab to replace standard floating-point layers with arbitrary-precision fixed-point layers (e.g., 6-bit or 8-bit quantization). Train using QAT (Quantization Aware Training).
2.  **Estimate Resources:** Use the **Surrogate Resource Estimator** (currently in Beta and under testing) to predict the hardware occupancy (LUT, FF, DSP, BRAM) of your quantized model on target FPGA boards before launching synthesis.
3.  **HLS Conversion:** Convert your QKeras model to C++ using **hls4ml**. Test and verify bit-accurate simulation outputs, and export the synthesizable project files (remote **Build Agent** automatic bitstream generation is currently under development for Pro accounts).

Now you are ready! Proceed to the [Dataset Format Guide](dataset-format.md) to prepare your data.
