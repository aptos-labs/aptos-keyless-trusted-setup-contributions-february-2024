# oidb-trusted-setup-contributions


The folder `contributions` contains all `.zkey` files output by the Aptos OIDB trusted setup. Each `.zkey` file corresponds to the contribution of one participant, so that i.e. `main_00004.zkey` corresponds to the output of the contribution made by participant 4. 

Each contribution may be verified by running the command 

```npx snarkjs@0.6.11 zkey verify main.r1cs powersOfTau28_hez_final_21.ptau contributions/<contribution_filename>.zkey
```


