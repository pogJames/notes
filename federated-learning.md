# FL algorithms

### FedAvg
*weighted averaging* local model updates
> baseline FL algorithm
### FedProx
*proximal term* prevents client drift
> handles non-IID data better
### SCAFFOLD
*control variates* correct local update drift
> more accurate but double computational cost
### FedNova
*normalizes local updates* by the number of local steps performed
> for clients with different computational power
### FedMA
*layer-wise* model aggregation, matching similar features layer-by-layer
> strong non-IID across clients -> server does more computation (matching/optimization per layer)
### FedSNGP
uncertainty-based client *clustering*, fleet-wide learning
> evaluate clients similarities -> group similar ones -> perform FL within clusters\
> global model still benefits from cross-cluster knowledge
