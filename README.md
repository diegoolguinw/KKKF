# KKKF - Kernel Koopman Kalman Filter

KKKF is a Python library that implements kernel Extended Dynamic Mode Decomposition (EDMD) of Koopman operators and provides a non-linear variant of the Kalman Filter. This library is particularly useful for state estimation in dynamical systems with non-linear behavior.

## Installation

You can install KKKF using pip:

```bash
pip install KKKF
```

## Features

- Kernel-based Extended Dynamic Mode Decomposition (EDMD)
- Non-linear Kalman Filter implementation
- Support for additive dynamical systems
- Integration with various kernel functions (e.g., Matérn kernel)
- Robust state estimation with noise handling

## Dependencies

- NumPy
- SciPy
- scikit-learn (for kernel functions)

## Quick Start

Here's a simple example of using KKKF to estimate states in a SIR (Susceptible-Infected-Recovered) model:

```python
import numpy as np
from scipy import stats
from sklearn.gaussian_process.kernels import Matern
from KKKF import AdditiveDynamicalSystem, Koopman, KoopmanKalmanFilter

# Define system parameters
beta, gamma = 0.12, 0.04

# Define system dynamics
def f(x):
    return x + np.array([-beta*x[0]*x[1], beta*x[0]*x[1] - gamma*x[1], gamma*x[1]])

def g(x):
    return np.array([x[1]])

# Setup system dimensions and kernel
N = 300
nx, ny = 3, 1
k = Matern(length_scale=N**(-1/nx), nu=0.5)

# Setup distributions
X_dist = stats.dirichlet(alpha=1*np.ones(nx))
dyn_dist = stats.multivariate_normal(mean=np.zeros(3), cov=1e-5*np.eye(3))
obs_dist = stats.multivariate_normal(mean=np.zeros(1), cov=1e-3*np.eye(1))

# Create dynamical system
dyn = AdditiveDynamicalSystem(nx, ny, f, g, X_dist, dyn_dist, obs_dist)

# Initialize Koopman operator and Kalman filter
x0_prior = np.array([0.8, 0.15, 0.05])
d0 = stats.multivariate_normal(mean=x0_prior, cov=0.1*np.eye(3))

Koop = Koopman(k, dyn)
sol = KoopmanKalmanFilter(Koop, y, d0, N, noise_samples=100)
```

## API Reference

### AdditiveDynamicalSystem

```python
AdditiveDynamicalSystem(nx, ny, f, g, X_dist, dyn_dist, obs_dist)
```
Creates an additive dynamical system with:
- `nx`: State dimension
- `ny`: Observation dimension
- `f`: State transition function
- `g`: Observation function
- `X_dist`: State distribution
- `dyn_dist`: Dynamic noise distribution
- `obs_dist`: Observation noise distribution

### Koopman

```python
Koopman(kernel, dynamical_system)
```
Initializes a Koopman operator with:
- `kernel`: Kernel function (e.g., Matérn kernel)
- `dynamical_system`: Instance of AdditiveDynamicalSystem

### KoopmanKalmanFilter

```python
KoopmanKalmanFilter(koopman, observations, initial_distribution, N, noise_samples=100)
```
Implements the Koopman-based Kalman filter with:
- `koopman`: Koopman operator instance
- `observations`: Observation data
- `initial_distribution`: Initial state distribution
- `N`: Number of samples
- `noise_samples`: Number of noise samples for uncertainty estimation

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Citation

If you use this library in your research, please cite:

```bibtex
@software{kkkf,
  title = {KKKF: Kernel Koopman Kalman Filter},
  year = {2024},
  author = {[Author Name]},
  url = {https://github.com/[username]/KKKF}
}
```