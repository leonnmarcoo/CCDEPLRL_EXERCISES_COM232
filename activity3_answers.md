# Assignment: Before and After Optimization Activity
### Dataset: Face Mask Detection (3 Classes)
> **Classes:** Improperly Wearing Facemask · Not Wearing Facemask · Wearing Facemask  
> **Training samples:** 428 | **Validation samples:** 107 | **Image size:** 128 × 128 × 3

---

## 1. Run the BEFORE Model

**Training Observations (Epoch-by-Epoch):**

| Epoch | Training Accuracy | Validation Accuracy | Training Loss |
|-------|:-----------------:|:-------------------:|:-------------:|
| 1     | 32.48%            | 44.86%              | 1.1248        |
| 2     | 42.99%            | 45.79%              | 1.0643        |
| 3     | 46.73%            | 52.34%              | 1.0370        |
| 4     | 51.17%            | 55.14%              | 1.0134        |
| 5     | 52.34%            | 58.88%              | 0.9910        |
| 6     | 53.97%            | 57.94%              | 0.9743        |
| 7     | 54.91%            | 55.14%              | 0.9572        |
| 8     | 57.71%            | 52.34%              | 0.9409        |
| 9     | 58.88%            | 59.81%              | 0.9226        |
| 10    | 60.51%            | 62.62%              | 0.9149        |

**Final Test (Validation) Accuracy: 62.62%**  
**Final Test (Validation) Loss: 0.8978**

---

## 2. Analyze the BEFORE Model

**1. Did the model learn fast or slow?**  
The model learned **very slowly**. After 10 epochs, training accuracy only reached ~60.51% — barely above random chance for a 3-class problem (~33%). Improvement was gradual at less than 3% per epoch on average, with no rapid jumps throughout training.

**2. Was the accuracy high or low?**  
The accuracy was **low**. A final training accuracy of 60.51% and validation accuracy of 62.62% is poor for a classification task, especially given that the dataset is relatively small and the classes are visually distinguishable. For reference, a random baseline would achieve ~33%.

**3. Did the loss decrease significantly?**  
The loss decreased **very slowly and marginally** — from 1.1248 down to 0.9149 over 10 epochs. This is a reduction of only ~0.21, which is minimal. The loss curve was nearly flat, indicating the model barely improved its confidence in its predictions.

**4. What do you think caused the weak performance?**  
Several factors contributed to the weak performance:
- **Poor optimizer (SGD) with a very low learning rate (0.0001):** SGD with such a tiny learning rate makes extremely small weight updates each step, causing the model to converge very slowly, or not at all within 10 epochs.
- **Insufficient model capacity:** The BEFORE model has only one hidden layer with 16 neurons, which is far too small to extract meaningful features from 128×128×3 (49,152-dimensional) image inputs. Most of the input complexity is lost.
- **No feature extraction:** Flattening raw pixel values and passing them into a tiny dense layer ignores all spatial relationships in images, making it nearly impossible to learn image patterns effectively.

---

## 3. Run the AFTER Model

**Training Observations (Epoch-by-Epoch):**

| Epoch | Training Accuracy | Validation Accuracy | Training Loss |
|-------|:-----------------:|:-------------------:|:-------------:|
| 1     | 42.29%            | 53.27%              | 8.0604        |
| 2     | 53.74%            | 54.21%              | 3.0413        |
| 3     | 65.19%            | 51.40%              | 1.0359        |
| 4     | 69.63%            | 57.01%              | 1.0983        |
| 5     | 61.45%            | 51.40%              | 1.2329        |
| 6     | 56.78%            | 49.53%              | 1.8549        |
| 7     | 70.09%            | 70.09%              | 1.0149        |
| 8     | 85.98%            | 71.03%              | 0.3261        |
| 9     | 87.38%            | 76.64%              | 0.3345        |
| 10    | 87.85%            | 78.50%              | 0.2952        |

**Final Test (Validation) Accuracy: 78.50%**  
**Final Test (Validation) Loss: 0.5486**

---

## 4. Compare Both Models

| Feature                  | BEFORE Model | AFTER Model |
|--------------------------|:------------:|:-----------:|
| Optimizer Used           | SGD          | Adam        |
| Learning Rate            | 0.0001       | 0.001       |
| Number of Hidden Layers  | 1            | 2           |
| Final Training Accuracy  | 60.51%       | 87.85%      |
| Final Validation Accuracy| 62.62%       | 78.50%      |

**Key Takeaways:**
- The **AFTER model** achieved a final validation accuracy of **78.50%**, compared to only **62.62%** for the BEFORE model — an improvement of **+15.88 percentage points**.
- Switching from SGD (lr=0.0001) to **Adam (lr=0.001)** gave the optimizer the ability to adapt learning rates per parameter and converge much faster.
- Adding a second hidden layer (128 → 64 → 3) gave the model significantly more capacity to learn non-linear patterns in the image data.
- The AFTER model showed **rapid learning after epoch 7**, where accuracy jumped from ~56% to ~86% in just two epochs — demonstrating the power of a well-configured optimizer.
- Both models still use raw flattened pixels rather than convolutional layers; further improvement (e.g., using a CNN or transfer learning) would likely push accuracy well above 90%.

---

## 5. Analyze the Graphs

### Which model learned faster?
The **AFTER model** learned significantly faster. From the Training Accuracy graph, the AFTER model jumped from ~42% to ~88% across 10 epochs, with its sharpest gains occurring between epochs 7 and 8 (a single-epoch leap of ~+16%). The BEFORE model, by contrast, crept upward by roughly 2–3% per epoch in a nearly straight line, ending at only ~61%. The AFTER model's steep curve clearly shows faster convergence driven by the Adam optimizer and greater model capacity.

### Which model had lower loss?
The **AFTER model** achieved a much lower final loss. The Training Loss graph shows the BEFORE model's loss declining from 1.12 to 0.91 — a shallow, almost flat slope. The AFTER model started with a high initial loss (8.06, due to Adam's larger initial weight updates) but dropped sharply to **0.2952** by epoch 10 — more than **3× lower** than the BEFORE model's final loss of 0.8978. On the validation loss, the AFTER model also ended lower (0.5486 vs. 0.8978), confirming it is genuinely learning better decision boundaries.

### Which model generalized better?
The **AFTER model** generalized better. Its final validation accuracy of **78.50%** substantially exceeded the BEFORE model's **62.62%**. Looking at the Validation Accuracy graph, the AFTER model's validation curve trends upward and closely follows its training curve in the final epochs (87.85% train vs. 78.50% val), indicating it is learning patterns that transfer to unseen data. The BEFORE model's validation accuracy hovered around its training accuracy the entire time, but both were low — meaning it failed to learn meaningful representations, not that it generalized well.

### How did the optimizer affect performance?
The choice of optimizer had a **decisive impact** on performance:

- **SGD (lr = 0.0001)** in the BEFORE model made uniformly tiny weight updates in a fixed direction. With such a small learning rate, the model barely moved in weight space per epoch, resulting in an almost flat loss curve and slow, marginal accuracy gains.
- **Adam (lr = 0.001)** in the AFTER model uses adaptive per-parameter learning rates, momentum, and RMS scaling. This allowed it to navigate the loss landscape more efficiently — making bold updates when gradients were consistent and smaller updates when uncertain. The result was dramatically faster convergence and a final loss more than 3× lower. Adam's higher base learning rate (10× that of the BEFORE model's SGD) also ensured the model made meaningful progress each epoch.

### Why is validation accuracy important?
Validation accuracy measures how well a model performs on data it has **never seen during training**. This is the true indicator of a model's usefulness in the real world. A model can memorize training data and achieve 100% training accuracy while completely failing on new samples (overfitting). By monitoring validation accuracy alongside training accuracy, we can:
- Detect overfitting early (training accuracy keeps rising while validation accuracy plateaus or falls)
- Choose the best model checkpoint
- Judge whether improvements in training accuracy reflect genuine learning or just memorization

In this activity, the AFTER model's validation accuracy consistently trended upward alongside training accuracy, suggesting it is genuinely learning — not just memorizing the training set.

### Which graph shows better optimization?
The **Training Loss graph** most clearly shows the difference in optimization quality. The BEFORE model's loss curve is nearly a flat, gently sloping line — a sign of an optimizer that is barely moving through the loss landscape. The AFTER model's loss curve shows the characteristic shape of good optimization: a steep initial drop (large early updates) followed by a more gradual convergence as the model refines its weights. By epoch 10, the AFTER model's loss (0.2952) is far below the BEFORE model's (0.9149), visually confirming that Adam with adequate model capacity optimizes the network far more effectively than SGD with a tiny learning rate.

---

## 6. Reflection

### How Optimization Improved Neural Network Performance

This activity demonstrated that building a neural network is only half the challenge — **how you train it matters just as much as its structure**. The dramatic performance gap between the BEFORE and AFTER models (62.62% vs. 78.50% validation accuracy) arose from three interconnected design decisions: optimizer choice, learning rate, and model architecture.

---

#### Effect of Optimizer Choice

The BEFORE model used **Stochastic Gradient Descent (SGD)**, the most basic optimizer. SGD updates all weights by the same fixed amount in the direction of the gradient. While reliable, it is sensitive to the learning rate and can be very slow to converge, especially on high-dimensional data like images.

The AFTER model switched to **Adam (Adaptive Moment Estimation)**, a significantly more sophisticated optimizer. Adam maintains a running average of both the gradients (first moment) and the squared gradients (second moment) for each weight. This allows it to:
- Move quickly when the gradient signal is strong and consistent
- Slow down and fine-tune when gradients become noisy or inconsistent
- Effectively give each weight its own adaptive learning rate

The impact was visible immediately: the AFTER model achieved in 3–4 epochs what the BEFORE model couldn't achieve in 10. Adam's ability to adaptively scale updates is especially valuable with small datasets, where the gradient signal per batch can be noisy.

---

#### Effect of Learning Rate

The learning rate controls the size of each weight update step. The BEFORE model used **lr = 0.0001**, which is extremely conservative. Each update moved the weights by a tiny fraction — useful for fine-tuning a nearly-converged model, but far too slow when starting from scratch. The result was the near-flat loss curve observed in the graphs.

The AFTER model used **lr = 0.001** — 10× larger. Combined with Adam's adaptive scaling, this allowed the model to make meaningful progress each epoch. However, the high initial loss (8.06 at epoch 1) revealed that Adam's momentum can cause large early updates; the model then self-corrected rapidly (loss dropped to 1.03 by epoch 3), illustrating how Adam naturally recovers from initial instability. By epoch 8, the loss had converged to a stable ~0.30 range.

The lesson is that the **learning rate must be paired appropriately with the optimizer**: a large learning rate with SGD causes instability, while a small learning rate with Adam wastes its adaptive capabilities.

---

#### Effect of Model Architecture

The BEFORE model had only **one hidden layer with 16 neurons**. When the input is a flattened 128×128×3 image (49,152 values), compressing all that information through just 16 neurons is a severe bottleneck. The model simply lacked the representational capacity to distinguish between three visually similar face-mask classes.

The AFTER model used **two hidden layers (128 → 64 neurons)**, giving it far more parameters and the ability to learn hierarchical representations. The first layer (128 neurons) can capture broad patterns; the second (64 neurons) can refine them into class-discriminative features. This architectural depth is what enabled the AFTER model to eventually reach ~88% training accuracy, while the BEFORE model plateaued at ~61%.

---

#### Overall Conclusion

Optimization is not a single knob — it is the combination of **what you optimize with** (optimizer), **how aggressively** (learning rate), and **what you are optimizing** (model architecture). Changing all three simultaneously in this activity produced a **+15.88 percentage point improvement** in validation accuracy and a **3× reduction in loss**.

For even greater improvements on this face-mask dataset, the next step would be to replace the flat dense layers with **convolutional layers (CNN)** designed to exploit spatial structure in images, or to apply **transfer learning** from a pre-trained model (e.g., MobileNetV2), which can bring validation accuracy well above 90% with minimal additional training data.
