# Neutron Diffusion Reactor Solver

Numerical solutions to the one-group neutron diffusion eigenvalue problem for a bare slab reactor, using finite differences, validated against analytical solutions and convergence analysis.

## Projects

- **Bare Slab Reactor** (`notebooks/01\_bare\_slab\_reactor.ipynb`): Finite-difference solution of the one-group neutron diffusion criticality eigenvalue problem for a homogeneous bare slab. Since the reactor is homogeneous, the fission source term is a scalar multiple of the identity matrix, so the generalized eigenvalue problem reduces to a standard one, solved with `numpy.linalg.eigh`. Validated against the analytical solution (k\_eff relative error \~7.3e-9, flux shape L² error \~1.04e-12 — near machine precision, matching the exact discrete-sine eigenvector property of tridiagonal Toeplitz matrices).

&#x20; An earlier version of this notebook had a grid-boundary alignment bug — grid points were placed directly at the physical boundary (x=0, x=a) rather than at interior points, misaligning with the finite-difference method's implicit ghost-boundary assumption. This produced a flux-shape L² error of 0.046 instead of the correct \~1e-12. The bug, diagnosis, and fix (`dx = a/(N+1)`, interior-only grid) are documented explicitly in the notebook.

- **Heterogeneous Absorber** (`notebooks/02\_heterogeneous\_absorber.ipynb`): Extends the solver to a spatially-varying absorption profile representing a localized control rod at the slab center. This breaks the homogeneous, Toeplitz-matrix structure of notebook 01 — no closed-form analytical solution exists, mirroring the transition from the infinite square well to the harmonic oscillator in the companion `schrodinger-solver` repository.

&#x20; Validated via convergence analysis against a high-resolution (N=4000) reference solution. The initial sharp step-function absorption profile gave a reduced convergence order (\~1.56) rather than the expected O(h²). This was investigated and attributed to the discontinuous coefficient violating the smoothness assumption underlying finite-difference truncation error analysis — confirmed via a controlled experiment: replacing the step function with a smooth tanh-based transition restored the convergence order to \~2.38, consistent with the hypothesis. The investigation, not just the final result, is documented in the notebook.

## Methods

Finite-difference discretization of the neutron diffusion equation, eigenvalue/eigenvector solving via `numpy.linalg.eigh`, convergence order analysis via reference-solution comparison.

## Key Insight

Across this repository and the companion `schrodinger-solver` repository, the same structural principle holds: constant-coefficient finite-difference operators are tridiagonal Toeplitz matrices with exact discrete-sine eigenvectors at any grid resolution, giving near machine-precision eigenvector accuracy. Introducing spatial variation (a potential in the Schrödinger case, an absorption profile here) breaks this structure, and both eigenvalues and eigenvectors then carry genuine O(h²) discretization error. Discontinuous spatial variation degrades this further, below second order, unless the discontinuity is smoothed.

## Requirements

Python, NumPy, Matplotlib, Jupyter

