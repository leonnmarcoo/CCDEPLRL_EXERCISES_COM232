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
