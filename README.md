# Scalable IP Fitting

Scalable Fitting Library for Inter-atomic Potentials.

The goal of this library is to provide an interface 
for fitting of ML-based interatomic potentials, allowing
parallelism over cores, nodes and GPUs as well.

This is intended to be achieved with outsourcing the
memory and computation heavy work to scalable libraries
like SLATE [1], initially with scope for direct linear solvers.

Models intended to be fitted:
- GAP [2]
- ACE [3] which is in Julia though, so through 
pACE even though that is lacking an essential part: multi-species support

links
-----
- [1] https://bitbucket.org/icl/slate/src/master/
- [2] https://github.com/libatoms/GAP
- [3] https://github.com/acesuit
- [4] https://github.com/ICAMS/pyace-lite and fork at https://github.com/stenczelt/ace
