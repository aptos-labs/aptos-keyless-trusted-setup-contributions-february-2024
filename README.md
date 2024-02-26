# oidb-trusted-setup-contributions


The folder `contributions` contains all `.zkey` files output by the Aptos OIDB trusted setup. Each `.zkey` file corresponds to the contribution of one participant, so that i.e. `main_00004.zkey` corresponds to the output of the contribution made by participant 4. 

Each contribution may be verified by running the command 

```npx snarkjs@0.6.11 zkey verify main.r1cs powersOfTau28_hez_final_21.ptau contributions/<contribution_filename>.zkey -v```

Upon completion, this will produce an output of the following form: 

```
[INFO]  snarkJS: Circuit Hash: 
		0a9aeb1f 247e8652 46a00128 ab2fbe10
		86ad8f2a 86a17f72 bd35f095 dfde1a86
		646f6faa fdaa46d6 9dde1ad5 88f21d4d
		0d4e9e74 519b561d 9e71e768 9c739b68
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: contribution #3 sherry-x-55457892:
		0125c8cb de936779 25ff1b62 49eff731
		15a68679 899678bb 83a19c0e cf8daf28
		20dcf4a9 78192394 2895cbc0 1ba661b3
		1628f395 f553b409 16428355 cfd53f6b
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: contribution #2 michaelaptos-158355435:
		63c5d60f 668bd588 d6c86d0e a459f24d
		d75c207a 76dbc07a 36c466c5 97ef3ba7
		760e6dd3 b3162767 313935eb afe8fd59
		1b4868b0 238ee70c beb60e3b a46a38d6
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: contribution #1 alinush-1724810:
		65c3e44a e8ae9dcd 2406d1b4 7413e09c
		72a6587b 5dbc6e00 ac607c84 cefbc4c1
		a9dd4d2c 39d78381 ea2a8cf2 8d3f4e91
		c32c2e8b eaf17465 b9bc8045 a529ecf1
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: ZKey Ok!
```

This is a list of all contribution hashes up to and including the current contribution that you have just verified. If you participated in the setup, you can verify that your contribution is being used correctly by running this command on the `.zkey` file corresponding to your contribution, and comparing the output contribution hash to the one which was printed out when you finished contributing during the ceremony. Note that the contribution hash is not simple a hash of the `.zkey` file.

TODO: Is the most recent contribution hash recomputed from the .zkey file? It should be.


