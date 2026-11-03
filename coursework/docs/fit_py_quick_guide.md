# `fit.py` — Quick Guide

Use these functions to load data, split it, train a neural network, and evaluate predictions.

## Functions

| Function | When to use | Example |
|---|---|---|
| `load(path)` | Read input `x` and target `y` from a CSV file. | `x, y = fit.load("data.csv")` |
| `split(x, y, n_train, n_val, seed=0)` | Shuffle and split data into train, validation, and test sets. | `fit.split(x, y, 60, 30)` |
| `_t(a)` | Convert a NumPy array into a PyTorch column tensor `(n, 1)`; mainly used internally. | `fit._t(x)` |
| `make_net(hidden)` | Create an *untrained* network. | `net = fit.make_net(20)` |
| `n_params(net)` | Count trainable network parameters. | `fit.n_params(net)` |
| `mse(net, x, y)` | Compute mean squared error (MSE), e.g. on test data. | `fit.mse(net, xte, yte)` |
| `train(x, y, hidden, ...)` | Create and train a network; return the network and loss history. | `net, history = fit.train(xtr, ytr, hidden=20)` |
| `predict(net, x)` | Return the trained network's predictions as a NumPy array. | `yhat = fit.predict(net, xte)` |

## Important settings

- **`hidden`**: `0` = linear model (straight line); `5` or `20` = that many sigmoid hidden units.
- **`epochs`**: number of passes through the training data (default `2000`).
- **`lr`**: learning rate (default `0.01`).
- **`weight_decay`**: L2 regularisation strength (default `0.0`).
- **`optimiser`**: `"adam"` (default) or `"sgd"`.
- **`xval`, `yval`**: optional validation inputs and targets; supply both together.
- **`seed`**: controls reproducible data splitting or network initialisation.

## Typical workflow

```python
import fit

# 1. Load and split
x, y = fit.load("data.csv")
(xtr, ytr), (xva, yva), (xte, yte) = fit.split(x, y, 60, 30)

# 2. Train and monitor validation error
net, history = fit.train(xtr, ytr, hidden=20, epochs=2000,
                         xval=xva, yval=yva)

# 3. Inspect and evaluate
print("Parameters:", fit.n_params(net))
print("Test MSE:", fit.mse(net, xte, yte))
yhat = fit.predict(net, xte)
```

## Which dataset is for what?

- **Training (`tr`)**: update the network's weights.
- **Validation (`va`)**: compare models and select settings such as `hidden` and `epochs`.
- **Test (`te`)**: evaluate the final selected model on unseen data.

**Notes:** `train()` calls `make_net()` itself, so you do not need to call it first. `mse()` and `predict()` use `torch.no_grad()` because no gradient calculation is needed for evaluation. In the provided code, `history["train"]` records loss **before** each update, while `history["val"]` records it **after** each update.
