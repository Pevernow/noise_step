# noise_step

Optimization and replication of noises_step.
In terms of results, it's very bad.
Perhaps I need to rethink its purpose

Some optimizations and reproductions of noise_step.
In terms of results, it's very bad.
Maybe I need to rethink its use

Use it directly in colab.
﻿
## Update
Added GPU implementation

Improved simple adaptive hyperparameters, convergence speed increased by four times

Improved block JVP, greatly reducing space usage

A small amount of exception handling mechanism

## Deficiencies
Overwhelming disadvantages:
Extremely high computational cost (time): Each training step requires computing (number of samples) JVPs, which makes its computational cost times higher than backpropagation (usually hundreds to thousands). For any model of practical size, this will increase the training time by several orders of magnitude and become impractical. 

Huge memory cost (space): Storing (or chunking) perturbation vectors requires huge memory. For large models (such as the Transformer with tens of billions of parameters), even with the chunking strategy, the peak memory required reaches TB level, far exceeding the capabilities of existing hardware. This is the most fatal bottleneck. S * Dim CS * Dim
Inefficient training: Gradient estimates are very coarse (based on signs and medians), and can converge much slower than SGD or Adam using exact gradients (from BP), and can converge to worse local optima, resulting in lower final model accuracy.

Poor scalability: The time and space issues mentioned above make this approach completely inscalable to modern large models.
Implementation complexity: While backpropagation is avoided, JVP, vmap, memory chunking, adaptive logic, etc. also introduce new implementation complexity.

Potential, but usually not overwhelming or practical "advantages" or research motivations:
Avoiding backpropagation: This is the core starting point of this approach. It may make sense in certain: specific or theoretical scenarios
Hardware limitations: If there is some computing hardware (such as some types of analog computing or future neuromorphic chips) that performs forward propagation or JVP very efficiently, but performs backpropagation (which requires storage of activations or accurate weight transfer) very difficult or impossible, then this forward-mode approach may be an alternative. But this requires specific hardware support to be beneficial.
Biological inspiration: Backpropagation is believed to lack a direct corresponding mechanism in the biological brain (such as the "weight transfer problem"). Studying such methods that rely only on forward calculations and local signals (although the JVP here is still a global operation) may help explore learning rules that are more in line with biology. But this is more of a theory than an engineering advantage. Theoretical research value

Concept exploration: Exploring different methods of gradient estimation is of research value in itself and can deepen the understanding of the optimization process.

Ternary weights/updates (Ternary):
Potential hardware efficiency (inference/storage): Ternary weights have small storage space, and theoretically multiplication during inference can be simplified to addition and subtraction. , The algorithm does not seem to use ternary operation optimization in the most time-consuming JVP calculation step, and only forces ternary operation during parameter storage and final update. Forcing the use of ternary updates throughout training may seriously damage model performance and training stability. A better approach is usually to train at full precision and then quantize. 

Implicit regularization: The large amount of randomness and rough gradient estimates during training may play a certain regularization role to prevent model overfitting. But this is usually not as clear and effective as using standard regularization techniques (such as Dropout, weight decay).

Summary:
For current mainstream deep learning tasks and hardware environments, this LK99-style algorithm. Its computational and memory costs are prohibitive, and the training effect and efficiency may also be far inferior to standard methods. There is almost no advantage in practice.
It is more like a research algorithm for exploring the possibility of learning under specific (usually hypothetical or future) hardware constraints.
Biologically inspired learning mechanism that does not rely on backpropagation.
Boundaries of gradient estimation and optimization theory.
It is completely unrealistic to regard it as a viable alternative to standard backpropagation, especially on large-scale models.
