# Machine learning

ML method = Algorithm + Strategy + Model(function)

ML model(application) = ML method + Data

## 1. Model

To make inputs map to right outputs, finding the closest function from hypothesis.

- Linear model
- Probability model
  - Frequency
  - Bayesian
- Generative model

## 2. Strategy

- Preprocessing data
  - Normalization: values in a range of [0,1]
  - Standardization
  - Centralization\Zero-mean
  - decorrelated data
  - whitened data
- Loss\Error function: for a single training sample
  - Mean Square Loss
  - Entropy Loss
- Cost function: for a total training set
  - Mean Square Cost
- Model selection
  - Performance ⇒ Model Generalization
    - Accuracy & Error
    - P-R curve: TP, TN, FP, FN
      - Precision
      - Recall
    - AUC curve
  - Regularization
    - R_emp → R_exp
    - R_erm = min R_emp
    - R_srm = R_erm + λ*Reg(f)
  - cross validation
- Activation function
  - Sigmoid
  - Tanh
  - ReLU
- Ensemble learning
- Reinforcement learning



归一化与标准化：https://blog.csdn.net/weixin_36604953/article/details/102652160



## 3. Algorithm

### 3.1 Optimization

- Gradient descent algo
- SMO algo

## 4. ML Method

- Linear regression
- Decision tree
- Naive bayes
- SVM
- Perception
- k-NN

## 5. Deep learning

Neural Network

- CNN
- RNN

# References

[Mathematics](https://www.notion.so/Mathematics-53c5f6d22c6f498cabcaca736558f7aa)

[CNNs](https://www.notion.so/CNNs-87009a2386d84dcc85f63c82b645d2f6)

[GANs](https://www.notion.so/GANs-d9d083517cef4994ad807f98051e0e1e)

cs231n

cs229